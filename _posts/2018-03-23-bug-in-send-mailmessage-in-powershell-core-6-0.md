---
layout: post
published: true
title: Bug in Send-MailMessage in Powershell Core 6.0
subtitle: >-
  Why does Send-MailMessage prepend the directory folders to attachment file
  names?
image: >-
  https://pi.tedcdn.com/r/talkstar-assets.s3.amazonaws.com/production/playlists/playlist_301/why_we_do_what_we_do_1200x627.jpg?c=1050%2C550&w=1050
---
## Simple, right?

So the other day I needed to automate a fairly easy task that some folks were doing manually. Download some files, merge some data with an existing static set of information, then send out an email. In fairly short order, this was all scripted out and working fine. As this task was very self contained, I decided to give it a whirl getting it up and running in a docker container. I've done some dotnet apps in containers, but I haven't really tried getting a script to work, but should be pretty easy.

It was! A little reading and after enough tripping over my own feet and typos and resolving command/version differences with the powershell script and all was working. However, just before I went to send out the notification that all was well (and it was as the container was just a test) I noticed that the emails coming from the container had incorrect file names.

Long story short, any files I passed to Send-MailMessage would prepend the folder path without the / characters. Weird. So to be clear, if you are emailing file.txt and it is located in /tmp/var/file.txt (I was using the microsoft/powershell linux container) the attachment in your email will be named tmpvarfile.txt. Super weird. 

![why?](https://finallygettingdowntobrasstacks.files.wordpress.com/2012/08/why1.gif)

I dunno why. But this does seem like a bug so I'm going to see how I can put an issue out on it. I found one request on stackoverflow, but no answer so this person was in the same boat.

What I did to work around this (even though I didn't really need to since this was just a test) was create a folder with a logical prepend value as the name. So if i was working on a the 'fancy' project and I wanted to send out report.txt, I would just put all of the files in a folder called `fancy-` and that way all of my attachments would come through as `fancy-report.txt`. I tested it and that worked fine, but not an option for many folks, I am sure.

This doesn't happen on my windows machine, but it *does* also happen on my mac when I run the scripts there. So it must be something in the powershell version, not just docker. I'm guessing the container and my mac are using the same or similar versions.

Here's a simple way to repro. I have these three files, a config.xml with some mail settings, a script to send the email, and a dockerfile to build (if you need one).

{% gist 3353edb7ce8f9e739ab9d0757118a201 config.xml %}

{% gist 3353edb7ce8f9e739ab9d0757118a201 job.ps1 %}

{% gist 3353edb7ce8f9e739ab9d0757118a201 dockerfile %}

If you save these in a folder you can run job.ps1 to see the results yourself (if you are on a mac or linux) or you can see it in docker. I ran this:

```powershell
docker build -t smmbug .
docker run -it smmbug
```

Here is the docker hub location for microsoft/powershell. 

https://hub.docker.com/r/microsoft/powershell/

I'll update this post if I ever find an answer, but I wanted to post it here so maybe someone else out in the world won't feel so lonely if they run into this issue. =P I'm sure that I could just use another method to send the email, but that doesn't mean it's not an issue. =)
