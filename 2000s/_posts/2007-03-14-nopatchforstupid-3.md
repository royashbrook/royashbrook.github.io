---
layout: post
title: nopatchforstupid #3
---

today's item of stupidity is the dynamic orderby clause. i see this particular abomination all over the place when i'm refactoring procs, reports, etc. firstly, why is this bad? firstly, most people include a null check and then build a dyanmic sql statement to execute. dynamic sql should always be avoided if possible. secondly, using this methodology leaves you vulnerable to injection attacks. finally for me, sorting can generally be done on the front end. normally the user wants to sort and resort etc, why would i want to sort it on the back end when they will just resort it 15 times on the front end. if you find that you are passing like sort by 15 different fields to a proc like this, i would generally tell you that you are stupid and need to rethink your "solution."

on to the code.

{% gist a831ff32902382de90170cc76b871f42 %}