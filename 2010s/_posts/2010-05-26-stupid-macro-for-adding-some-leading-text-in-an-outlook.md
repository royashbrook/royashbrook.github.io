---
layout: post
title: stupid macro for adding some leading text in an outlook window
---

so since i have looked this up and figured it out (i know... there isn't much to figure it out, but there it is) i figured i should blog about it so i can quit looking it up somewhere else.

basically i just needed to hit a button/hotkey int outlook and have it insert the time/date stamp and my name and a couple of pipes to just divide everything up. no biggy. but if you never create macros and do this once every two years, it's a bit of a pain to remember how. this is for outlook 2007, i think 2003 was a bit different.

basically you just create a new macro, and then you can add a button and/or bind a hotkey to that. here's this sub will insert something like the following:

`2010-05-03 10:34:54 AM EST | Roy Ashbrook | `

I have a prefix of vbcrlf on the line so i can just go to the end and hit the button. i use this to update tasks in outlook so i can remember when i added something. well, i guess not 'remember' since i just read it. 

anyway, here is the the sub that i use.

{% gist d62e4f50c5755b123c9b8b3ef5c638b1 %}