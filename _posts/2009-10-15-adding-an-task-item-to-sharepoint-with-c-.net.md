---
layout: post
title: Adding an task item to sharepoint with C# .net
---

There are lots of ways to do things like this. Here's a pretty simple way with c# and .net. forgive some of the using shortcuts, i was just isolating a couple of areas. and as always i normally just post these so i don't forget =P

this is just a console app to add a single item to a task list in sharepoint. very simple. the only thing that was a bit of a pain is you need to find your sharepoint id for the person. the easiest way i found that for myself was simply to go to the list, edit the view settings, select the assign to column and change the 'show' to id. then i grabbed that id and used it to create my items. if i had other people sharing this i would probably just build a small table and do lookups. if it was a really huge amount and critical, you can create a web service. there are a bunch of things out on the web about that but they all require server logic which i didn't want.

"sp" is a web reference to the asmx url below. seems that if you don't set the url property it won't work though. for all i know you can refer to a diff url for the web ref as long as you set the correct property. i didn't fiddle with that much.

{% gist 586e4c3bcf78bd4cf37fdcb9dc622110 %}
