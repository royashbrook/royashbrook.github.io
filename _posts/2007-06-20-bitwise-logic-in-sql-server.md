---
layout: post
title: bitwise logic in sql server
---

so I saw this headline the other day in one of my feeds:

> [Speed Up Performance And Slash Your Table Size By 90% By Using Bitwise Logic](http://sqlblog.com/blogs/denis_gobo/archive/2007/05/29/test.aspx#1349)

this, of course, piqued my interest. after reading this article, i decided to do a little research. one thing to realize is that using the int datatype you can only utilize 32 choices. i'm not an expert in using base 2 for math, however i went out and tried to find some articles that would refresh my memory. the following two were my favorites:

[Bitwise Operations in C](http://www.gamedev.net/reference/articles/article1563.asp)

[PHP Bitwise Tutorial](http://www.litfuel.net/tutorials/bitwise.htm)

as you can see by the comments in the other guys blog, not everyone agrees with this 'clever' idea. i have to admit that it is definitely more work to ensure that your developers are familiar with bitwise calculations in general. i think it would be fun though if you have some type that could be multiple choice. one commenter raises the idea that in theory using 8 bit fields instead of 1 int field with bitwise logic should take up the same storage space. definitely an interesting way to store data. not to mention, if you aren't using sql. maybe you want to use xml or some other method of storage. you could probably even serialize some custom object. that roams a bit far away from a pure sql conversation, but.... well whatever.

i thought it was definitely interesting enough to share. 😀 
