---
layout: post
title: how do i find what nt domain groups i'm in?
---

i've received this question from many developers who either weren't sysadmins ever or just forgot. some, perhaps, are just lazy and don't want to look it up or remember it. it may be that others still just want to see if *i* remember. =P

the command line to view what nt groups you are in is, literally:

` net user %username% /domain `

include the % symbols and it will use the environment variable for your username.