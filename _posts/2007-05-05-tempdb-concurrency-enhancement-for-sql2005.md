---
layout: post
title: tempdb concurrency enhancement for sql2005
---

This tip is the pimpness. There are really no downsides and it can help a lot if you are having heavy contention with your tempdb.

http://support.microsoft.com/kb/328551/en-us

here’s a summary of what you do:

Increase the number of tempdb data files to be at least equal to the number of processors. Also, create the files with equal sizing.

That’s pretty much it. Definitely read the article though as it has a ton of great technical information about why this works and what is going on in a situation where something like this is needed.

In general I would probably say if you *need* this fix, you really *need* to revisit why you are running a complete piece of crap application in a production environment, but that’s just me. =D