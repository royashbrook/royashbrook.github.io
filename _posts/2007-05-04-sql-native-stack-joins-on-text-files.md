---
layout: post
title: SQL native stack joins on text files
---

snipped from [here](http://blogs.msdn.com/ashvinis/archive/2005/10/24/484274.aspx).

> Openrowset now allows the BULK clause to be specified which accepts a filename and format specification. An OLE-DB driver for flat-files or XML would also do the job but this is a nice work-around for the native stack. The only difficulty is getting your head around the format for the .fmt file but after that it 'just works':

``` SQL
    select * 
    from openrowset(bulk N'c:\temp\test.txt', formatfile = 'c:\temp\test.fmt') as x
```