---
layout: post
title: basic sql load testing with a multithreaded c# console app
---

this is a very basic load tester. it basicaly lets you from the command line specify what command to run, how many times, against what connection string and whether you want to see any results. obviously, you can tweak it to print the results differently as you see fit, currently it just spits out field numbers and a value.

here's the code:

{% gist d8c455444de15819decdd9761603efa0 %}

sample command line syntax:

TestSQL.exe c:\test\t.sql 100 "server=localhost;database=zipcode;integrated security=sspi;Pooling=False;connect timeout=0;" false

i used the same tsql in the t.sql file that i used in [this post](http://www.drowningintechnicaldebt.com/blogs/royashbrook/archive/2007/03/19/testing-with-a-random-id-in-sql.aspx).