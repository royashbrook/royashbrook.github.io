---
layout: post
published: false
title: Quick Commands for RasPi Setup
---
Just a place to save some commands I frequently use when working with raspis.

```bash

#update
sudo apt-get update && sudo apt full-upgrade -y

#setup passwordless access
# src https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md
ls ~/.ssh
ssh-keygen
ssh-copy-id pi@raspberry


```
