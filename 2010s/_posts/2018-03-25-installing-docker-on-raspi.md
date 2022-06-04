---
layout: post
published: true
title: Installing Docker on Raspi
image: >-
  https://img00.deviantart.net/4230/i/2012/059/9/1/happy_pie_by_katiiia-d4rc78t.jpg
subtitle: For what it's worth...
---
This one was super easy. There is a one line install on the [raspi page for this](https://www.raspberrypi.org/blog/docker-comes-to-raspberry-pi/).

`curl -sSL https://get.docker.com | sh`

I also found a great install page [here](https://gist.github.com/tyrell/2963c6b121f79096ee0008f5a47cf347)

For me this didn't work and I got a couple of errors that basically seemed to indicate that the docker service wasn't running. Or I should say it didn't work right off the bat the way I thought. I could run docker version, but i couldn't docker run anything.

After a little looking, I came to [this](https://github.com/moby/moby/issues/30917) page and someone mentioned rebooting. So simple. So elegant. So correct. After a reboot everything was working for me. However, prior to rebooting I also ran `sudo systemctl enable docker` to make sure that docker started up after the reboot as I saw that noted in a few places.

Now, the only caveat is that you need arm specific images. So `docker run microsoft/powershell` didn't work but `docker run armhf/hello-world` works fine. You can get around this by using Kubernetes cluster and probably a variety of other ways where you build the image somewhere else and then run it on the rapi.

[Here](https://www.hanselman.com/blog/HowToBuildAKubernetesClusterWithARMRaspberryPiThenRunNETCoreOnOpenFaas.aspx) is a great article on doing something like that.


