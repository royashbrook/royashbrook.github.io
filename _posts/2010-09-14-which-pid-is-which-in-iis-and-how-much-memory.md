---
layout: post
title: which pid is which in iis and how much memory are they using
---

You can do this lots of ways. Here's a quick way from the command line:

```shell
iisapp
tasklist /FI "IMAGENAME eq w3wp.exe"
```
 
then just line up the PID numbers.