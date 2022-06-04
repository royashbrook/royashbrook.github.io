---
layout: post
title: passing csv to sql is still bad
---

so about… oh… 5 years ago, I posted this blog entry:

http://www.drowningintechnicaldebt.com/RoyAshbrook/archive/2007/03/09/passing-csv-to-sql-is-bad-mmkay.aspx

I recently needed to do something where I was passing in something like a csv to a sql proc and was reminded of it, so I looked it up. I noticed that I really didn’t have a good code sample on the old blog. I happened to notice a similar question on stackoverflow from 2010:

http://stackoverflow.com/questions/2312581/comma-delimited-sql-string-need-to-separated/15668344#15668344

So, I thought now would be a good time to post an updated code sample of this. Below is a method of simply passing a string to check (bad) and a way to pass that same value in xml (good). There are lots of ways to split the strings and do other things, but I like this way. Of course you can do this in LINQ now with a .contains pretty easily, but this is a good way to do it in pure sql.

{% gist e9586150294e1564ef183fd4d2e63613 %}
