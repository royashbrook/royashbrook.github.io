---
layout: post
title: running wss 2 and wss 3 side by side
---

it's pretty easy. i think that there are probably several methods you could use to do this, but this how i do it.

if you have 2 or 3 installed, remove them.

make sure you have .net 2.0 and 3.0 installed.

make sure you have your asp.nets marked as allowed in iis.

install 3.0, take all defaults.
in v3 central admin site, delete the default site, delete site and db
install 2.0, take all defaults
test 2.0
in v3 central admin site, create new, change db and use a unique url or port
test 3.0 and rejoice

the end.