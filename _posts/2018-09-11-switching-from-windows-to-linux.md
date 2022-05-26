---
layout: post
published: false
title: Switching from Windows to Linux
---
## A New Post

Download cinnamon 64bit mint as iso, 1.8+GB so be aware

use etcher to burn to ssd usb3 external drive (i am using 480GB drive to test working from the USB drive only)

interrupt startup to boot from usb and in desktop in just a few moments. immediately get a popup that wifi is available

Look in internet, don't see chrome, which i want to use. open up software manager search for chrome, only see chromium. hmm... head out to google and go to the chrome download a few clicks and next/next/next dialogues and chrome is in. Sign in and all of my plugins popin shortly. yay! Everything appears to work. No surprises here.

as I use powershell for a lot of current development work, let's go get docker, powershell, and vscode.

vscode, no issues, just hit download site and download and install

powershell, no major issues, just had to check for which ubuntu version this mint used and download the appropriate package. installed with no issues. when in terminal, couldn't run 'powershell', but after looking at instructions discovered i needed to run pwsh-preview instead. worked like a champ. although i can just use powershell with docker, i wanted to install it to test and i *think* i will need it to do interactive scripting in vscode, but will see.

docker, i just go out to docker site and follow instructions for ubuntu and do this from terminal. this causes me a little heartache since 'mint' returns the wrong version and after reviewing the instructions more, i see that it specifically says 'mint' will need to have the correct ubuntu version put in. in my case, it's 'bionic' so i just put that in the line of the command instead, and away we go. unfortunately, running hello world doesn't work. i'm thinking at this point it may be due to running off usb.

let's try onedrive. sadly, doesn't look like there are a lot of tools for onedrive. looks like rclone and a tool called onedrive-d are what is available. I'll give onedrive-d a whirl first as i need to install git anyway. but before that, let's try rebooting to see if anything sticks.

sadly no, but that's ok, i just wanted to test run. so now i am running the 'install linux mint', i decide to choose 'something else' instead of installing it along side and try to install it to another usb drive i have.

ok, so after rebooting. everything looks great. have to reinstall stuff
-chrome
-vscode
-git
-docker (no issues this time when using the right version and no aufs issues)
-powershell (just docker pull and run -it this time, works fine)

so these are just the defaults. i'll have to have more impression information after some use.



Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
