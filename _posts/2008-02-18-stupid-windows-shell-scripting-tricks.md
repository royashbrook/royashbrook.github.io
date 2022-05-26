---
layout: post
title: Stupid Windows Shell Scripting Tricks
---

If you've never used the 'for' command in windows, it is your friend. It longs for you to use it! It is 'Da Bizzle' as the kids say. Here's a silly little example of using it to delete all of the temporary internet files from all of the profiles on a machine.

``` sh
cd "C:\Documents and Settings"
for /f "tokens=*" %i in ('dir /b') do rd /s ".\%i\Local Settings\Temporary Internet Files\" 
```