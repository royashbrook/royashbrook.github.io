---
layout: post
published: true
title: Setting up Raspberry Pi Zero redundant PiHole Ad Blocker
image: 'https://upload.wikimedia.org/wikipedia/commons/e/eb/Adblock_logo.png'
subtitle: Block Ads on your Network
---
I am a big fan of [pihole](https://pi-hole.net/). I had used it a little bit off and on for a couple of years a while back, and finally implemented it as a permanent part of my home network a couple of years ago. I've been a happy camper ever since.

I first ran it on a raspi 2, then later on a 3. It ran great with no issues, but since I was only running it on one raspi, I set my secondary DNS on the network to a google dns server (8.8.8.8) just in case the pihole had issues.

Recently I was fiddling with [docker swarm](https://docs.docker.com/engine/swarm/) on raspi and decided to repurpose my raspi 3 for that effort and solve my redundancy issue at the same time by switching over to a pair of raspi zeros for pihole.

Now, it's worth noting I didn't have a lot of the requirements that I have seen online for setting up dual piholes where block lists are shipped back and forth etc. I am using a vanilla pihole setup based on the 80/20 thing where I get most of the value by just plugging these in. So I get the same reward with redundancy by just using two. So this is pretty much a stock pihole setup x 2 and then modifying the dhcp server (the router) to hand out the two pi zeros as the dns servers. As always, YMMV. =)

{% gist 7f2e1d287ffea9549afe31ad98e2935b %}

My hope is to actually get this running on a docker swarm, but so far I haven't gotten that to work. Was easy enough to get the pihole running via docker, but want it to run on swarm. Hopefully there will be a follow up post on how to get that working at some point, but for now this got me me some benefit.

In my case, I have the pizeros plugged into something like [this](https://www.amazon.com/Anker-PowerCore-Portable-Charger-Foldable/dp/B01K702S66/) so I get some power redundancy as well for cheap.
