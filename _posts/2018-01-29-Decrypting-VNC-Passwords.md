---
layout: post
title: Decrypting VNC Passwords
---

Situation:

I have a very old 'server' that is running vnc. No one knows the password, but we have local admin on the machine. The machine is, however, located in a remote place. How to recover the password?

Answer:

1. Since I have local admin on the box, I was able to get the encoded password from this location:

``` powershell
reg query "\\myserver\hklm\software\ORL\WinVNC3\Default" /v Password
```

2. I was able to use [this](http://aluigi.altervista.org/pwdrec/vncpwd.zip) file to decode by just copy/pasting the value from above.

That's it. =) After that I used vncviewer to get into the box and everything was fine.


I was also able to find some github projects, but I don't have gcc on my windows machine, so I just used the tool from above as I found some references to aluigi.org writing this one. I'll include a couple github projects here, however.

- https://github.com/jeroennijhof/vncpwd
- https://github.com/trinitronx/vncpasswd.py
