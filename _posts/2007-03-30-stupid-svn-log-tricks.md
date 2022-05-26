---
layout: post
title: stupid svn log tricks
---

ok, this is way lame, but it's just a simple poc =P this is a winform app that will launch svn log and all of the files you drop onto it or sendto it and put them all in one window. i also put a little bit of basic logic to remove blank lines and the seperators from between the log entries. the only purpose of this is if you have to regularly get these comments back for about 10 or 20 files, using multiple threads will be way faster. hopefully this will help someone else who needs a basic example of a multi-threaded command line app call =P of course, you can substitude svn log for any command line you wish, or convert it to a command line and feed it a list file. pretty much, whatever. note that this is for a winform app with a form named form1 and a richtextbox named richtextbox1. very unfancy =P

here's the code:

{% gist a6fdb1243698fc0d236f76a7184d4c04 %}