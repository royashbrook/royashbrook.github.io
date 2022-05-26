---
layout: post
title: Adding a task item to sharepoint with VBScript and Microsoft.XMLHttp
---

who'd want to such a crazy thing? well me. i just wanted to find the dirtiest quickest way with zero overhead to push data directly into a sharepoint list. i found a few articles with some information about this, but the closest one was this one using jquery. so here it is in vbscript with no extra libraries or anything (except what is build into windows which is still plenty). one cool thing about this is it was quite a bit faster than the .net method i posted previously. i attribute that to building a nice web reference with all the different methods in it and all that overhead. i'm sure i could speed it up, and i didn't try just using this method in .net which would probably be faster too. this doesn't require .net or a compiler though and i can just open the file to fiddle if i need to so nifty for my current purpose. =D

and yes, i know there are better ways to concat text. i'm done fiddling with this for now so we'll all live =P code is commented below i believe sufficiently. it's not too complicated =P

{% gist 4d3f98e20ce106b9b87d856a6e70d228 %}
