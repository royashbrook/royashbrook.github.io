---
layout: post
published: true
title: The fastest way to get a folder size in windows
image: >-
  https://thumb7.shutterstock.com/display_pic_with_logo/709372/105446477/stock-photo-comic-fast-running-superhero-illustration-of-a-cartoon-happy-super-hero-running-faster-than-a-105446477.jpg
subtitle: Dir size fast...
---
![tortoise and hare](https://www.iconspng.com/images/tortoise-and-hare-fast-and-slow/tortoise-and-hare-fast-and-slow.jpg)

I tested this on a folder with about a half a million extremely random files from a very old system I cleaned off. I wanted to see how much space was saved but right clicking on the folder was brutally slow.

This was super fast. Ridiculously fast.

`robocopy /l /nfl /ndl c:\folder \\localhost\C$\nul /e /bytes`

Source article is using powershell to pretty up the info, but the function listed didn't work, so i just ran the powershell command. Information was at the bottom so easy enough to do my own prettification.

`text
                  Total       Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         3704         3704         0         0         0         0
   Files :       452585       452585         0         0         0         0
   Bytes : 192119912385 192119912385         0         0         0         0
`

Then easy enough to just divide to get the number at the command line like `192119912385/1024/1024/1024` to get `178.925611437298` in GB. You may prefer to just divide by 1000s instead of 1024. Dealer's choice. =)

[source](https://www.linkedin.com/pulse/technical-thursdays-get-directory-sizes-stupidly-fast-carlos-nunez)
