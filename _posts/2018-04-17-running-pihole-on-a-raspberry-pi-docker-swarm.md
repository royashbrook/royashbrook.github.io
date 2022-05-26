---
layout: post
published: true
title: Running PiHole on a Raspberry Pi Docker Swarm
image: >-
  https://vignette.wikia.nocookie.net/simpsons/images/5/5e/Stewie_griffin_by_mighty355-d7u8ws6.png/revision/latest?cb=20161111175109
subtitle: Victory Pi(Hole)!
---
OK, so this was a bit entertaining. There are a ton of articles out there on how to get a Docker Swarm running on a RasPi cluster. Basically, just get all of the Pis up, updated, and setup with static/reserved IPs, then install docker, then run the docker swarm init command and join the workers as you have them. Yay, now you have a docker swarm.

In my case I wanted to get PiHole running on the swarm. Fortunately, there is already a docker image for that [here](https://hub.docker.com/r/diginc/pi-hole/). They provide a docker run command, but not a docker service command, so we'll have to deal with that.

There are several bad/good news items regarding this setup. I'll break them down into a few areas.

## IP Addressing

The bad news was that docker doesn't appear to be able to create a real Virtual IP. Meaning you can't just have a VIP published out there and have it round robin to different hosts. The good news is that docker swarm already kinda does this with services. If you can publish something as a service, it will basically accept traffic on *any* host in the swarm on that port and route it appropriately.

That's grand, the only bad thing is that I really wanted to run TWO piholes for backup, but you can only publish one service on that port on the swarm. The good thing is that if you publish the service you can really hand out any two IPs in the swarm and it will work. So as long as those two machines don't go down, you are good. This is basically the same situation you have with two single machines from a failure perspective, but from a load perspective, the swarm will load balance everything anyway. You *can* simply setup one service replica and it will bring it back up, but since it's DNS, I wanted to make sure it was up so I hand out two IPs and have it set to two replicas. The IPs don't actually matter as they could be any in the swarm, the important thing is that there are at least two.

If we were using named services or something we could give DNS entries to, there are a ton of load balancers/reverse proxy solutions out there for solving this problem. I couldn't do that with DNS since you are handing out an IP. It would be nice if you could create a public VIP that any/all of the hosts could answer, but that doesn't appear to be a thing that will currently work since the IP has to be associated with *something* for routing. I think you can use the macvlan networking if you want to do this in some fashion, but I decided to just go the service route and hand out a couple of IPs from my swarm and let docker do the hard work.

In my case, I have 4 raspis with 192.168.2.201-204, so I just picked .201 and .202 for the DNS IPs to hand out on my LAN. You can actually go to the others and it works fine too, but my router is only handing out two IPs so... that's how it is. =) (we'll talk another day about getting DHCP setup like this and then handing out all 4 IPs, muhahaha!)

## Persistant Storage

So now the IP issue is good, the next problem I ran into was storage. I really didn't care about maintaining persistant state info, but on occassion in the past I have added a DNS name, so I decided I should have somewhere to put that just in case. So on all of the hosts, I created local folders to mount to so the pihole could save it's information there. These aren't replicated/consistent, but if I wanted to I could manually replicate some information there. I'm not doing that right now though, so in this case the bad news is that I didn't have an immediate way (although there are some) to just get persistant state setup if I wanted to. The good news was that I didn't really want/need to and I could have a middle ground of just storing it on each host that might end up with the service and it would work fine.

It's worth noting here that I am no docker (swarm or otherwise) expert, but there seems to be some way to do something with volumes, but I couldn't see how this worked. It also appears there are systems like gluster, ceph, rexray, and others for solving this kind of problem. I don't need to solve that today.

So the solution for this was basically `mkdir /home/pi/pihole /home/pi/dnsmasq.d` on each node as those were the folders I was going to mount to. Worth noting these could be anywhere as long as the service has access, but this is what I did.

## The Docker Service Command

So this one was a huge pain for me, but it ended up being a very simple solution. I am supposing I could have eventually read all of the docker service command documentation and maybe I would have found this, but it is far more likely that I would have just looked for a way around this or moved on to some other solution. Ah, the *problem* was that I would try and run the docker service command after translating the docker run command and it would tell `no suitable node (unsupported platform on 4 nodes)` no matter what I tried. I tried various images, changing the OS ID and a number of other little odd tricks. I could actually run the same image using docker run with no issues, but docker service was a no go.

Finally while googling, I came across [this](https://github.com/docker/swarmkit/issues/2294) issue and the quote below from [hero of the day](https://github.com/nishanttotla), my man Nishant!

> @trunet I'm not sure about that, but we'll see what's the best way to resolve this. Until then, you can get around this issue by using the `--no-resolve-image`flag on service create/update which will not add platform information to your service spec.

Boom! In business. This finally yielded the working command I went with:

```sh
docker service create \
--name pihole \
--env ServerIP=192.168.2.201 \
--publish 53:53/tcp \
--publish 53:53/udp \
--publish 80:80/tcp \
--replicas=2 \
--no-resolve-image \
--mount type=bind,src=//home/pi/pihole,dst=/etc/pihole \
--mount type=bind,src=//home/pi/dnsmasq.d,dst=/etc/dnsmasq.d \
diginc/pi-hole-multiarch:debian_armhf
```

Note I am just telling the pihole about a single external address, but that doesn't seem to make any difference if I am running 1 or 10 replicas. Well, I didn't try 10, but I did do 5 which doubled up on one host, and it still seemed to work fine. Generally, I just left it running two. I think I could probably modify the image or just the startup scripts to somehow autodetect an external host and avoid setting the serverip, but it worked fine this way so I'm good with it for now.

## Caveats and Notables

When I first got the service working, I only created the folders on one machine and I set the IP to that host IP and put a constraint in to only start on that host. But after creating the folders on all of the machines, it could run anywhere and regardless of what I told the pihole service what it's serverIP was, it seemed to work fine as it would answer any calls that came in.

When viewing the pihole admin, you'll get routed to random machines depending on how many you have up. I didn't care about this, but I noticed that if I had two replicas running, the load *REALLY* got load balanced and evenly distributed. This is different from when I was running two physical piholes as the primary would have almost all of the load and the secondary just a little bit. Now if there are 4 hosts, they will all have an identical amount of load pretty much. Very cool! Free load balancing!

If you did want to have 2 TRUE pihole type devices running, I think you need two swarms to make things easy. I mean, these are only raspis, so if that's important to you, it's cheap enough to do. Maybe there is a way outside of docker swarm (I mean, I know there are) to create a vip and then load balance it to different hosts on the swarm, but that kind of defeats the purpose for me as we are adding a load balancer/vip just so we can avoid running on one port. I'm not sure why that would matter anyway as you could just use some kind of persistant storage and, to me, for a pihole, this isn't really important. The ads are blocked and it is on the docker swarm so it's got some pretty decent fault tolerance compared to once device and it can actually be more responsive due to the load balancing, I would think.

I wonder if maybe you could split the UDP and TCP 53 traffic into two services. Probably not going to mess with that now, but maybe sometime.

### Some of the many source documents I used.
https://github.com/docker/swarmkit/issues/2294
https://hub.docker.com/r/diginc/pi-hole/
https://blog.hypriot.com/post/how-to-setup-rpi-docker-swarm/
https://www.techrepublic.com/article/how-to-share-data-between-a-docker-container-and-host/
https://www.nico-maas.de/?p=1525
https://www.raspberrypi.org/forums/viewtopic.php?t=176445
https://stackoverflow.com/questions/39071626/docker-swarm-overlay-vip
https://semaphoreci.com/community/tutorials/consuming-services-in-a-docker-swarm-mode-cluster
http://blog.scottlogic.com/2016/08/30/docker-1-12-swarm-mode-round-robin.html

![victory is mine](https://cdn-images-1.medium.com/max/280/1*1rMVnfCZPbJh_AupNyem-w.jpeg)




