---
layout: post
published: true
title: Get Windows Update Info via Powershell/COM
image: >-
  https://thumbs.dreamstime.com/b/update-status-blue-color-rubber-stamp-46437763.jpg
subtitle: A Difficult Simple Thing
---
I have recently needed to check what windows updates have been applied to a number of machines. What I ended up using was something like this:

{% gist c1f68834d72864fa623057d1c388821e Get-WindowsUpdates.ps1 %}

You can expand all of the commands out to look at things, but basically we are looking at the windows update service/session, grabbing the history, and then looking at the things that were installed and organizing them by date to see them. That's in the scriptblock, then we are calling that command on a number of the machines at the same time and waiting on thsoe jobs to finish. If you have any messed up computers, like i did, they won't work and you'll get red errors on the screen unless you pretty things up, but this got the job done for me.

I found a couple of ways to do this, they are down below, you can get data from the update service or you can look at the history. I don't know why but the history was _*way*_ faster for me, so that's what I used. There are also questions of what is hidden, etc, that I'm not going into. There seems to be some naming differences as well. Regardless, this worked for my purposes so here you go. =)

{% gist c1f68834d72864fa623057d1c388821e more.ps1 %}

Sources:
- https://gist.github.com/Grimthorr/44727ea8cf5d3df11cf7
- https://social.technet.microsoft.com/wiki/contents/articles/4197.how-to-list-all-of-the-windows-and-software-updates-applied-to-a-computer.aspx
- Like a million others as I tried to find a faster/easier/simpler way to do this. Seems like this should be pretty simple, but it wasn't. Also when you have a lot of different versions of operating systems, that causes a lot of drama. Solution above was 'good enough' so in the name of YAGNI, I left it here.
