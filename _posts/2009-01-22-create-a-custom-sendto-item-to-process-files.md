---
layout: post
title: create a custom sendto item to process files
---

so i have this video camera and it records files to mod files. i dunno why. mod's are just like mpegs. you can just rename them and open them. i don't know why they aren't just saved as mpegs, but whatever. so i got tired of dropping out to dos and just renaming the files so i could review them. i could also i'm sure find a way to just register mod files with my computer but bah. i decided to just create a custom sendto item so i could just mass rename them. so if you want to rename a bunch of files or really do anything to a bunch of files, you can create a file called whateveryouwant.cmd and put it in your sendto folder inside of your profile folder. then i used the text:

```sh
:loop
ren %1 *.mpeg
shift
if %1. NEQ . goto loop
```

in the file. you could replace the ren command with whatever you want. %1 should be the current file. anyway, if anyone finds this and it doesn't make sense, just comment and i'll clarify.
