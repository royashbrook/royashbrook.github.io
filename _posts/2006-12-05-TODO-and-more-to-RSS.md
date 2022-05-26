---
layout: post
title: TODO and more to RSS
---

inspired by [this](http://www.benhammersley.com/code/code_todo_lines_to_rss.html) article, i decided to write a .net version. i thought this could be something useful for searching our code base at work. kind of the same as this guy has setup for his single machine, but i figured i could search a whole subversion tree and pipe it to our dev intranet site.

Since I was at it, I figured I would make it a little more flexible. You can set all the options through the querystring. if you don't like that, feel free to just set them manually. i didn't want to pass around the xml object, so i just made it static. looking at this, i feel like there is a better way to do this, but i can't put my finger on it at the moment and it works fine. i skipped doing full regex and only match lines that have the requested item. i also skipped returning all the matching lines in a single rss entry and i just return every line match in it's own item. if i didn't do that, i had to kind of pick what format to use internally for the matches and i decided i'd let someone do that with a style sheet.

enjoy!

{% gist f453d2d8a2fa46711cfa1997ebd89e05 %}
