---
layout: post
title: ring buffer in tsql
---

i found this post recently. no idea what i was looking for when i found it, but there it is. basically, the post is about having an application that will show a random user in the top right of the screen. i'll let you read the rest, but i was thinking of what i would do if i wanted to do something like this. i think the approach of adding all of these functions is overkill. based on the information in the article it seems that the goal is to lower the expense of the query. i personally feel like this shouldn't be that expensive. i suppose if you were to select * and just order by random, that would possibly be slower, but why not just select the id you need first, then get the record you want? that seems to go much faster for me.

let's say we *did* want to create a ring buffer in tsql though. a better way i think would be refresh the ring at a set interval, and have the ring move things to the 'end' of the ring after they were selected. you could actually build a method for this, but i'm just going to use triggers. i'm not a big fan of triggers personally, so don't take this as an advocation of using them a lot, it just makes this example very easy. in this case, we're essentially reproducing a certain type of object and some certain behaviour in tsql. here's some sample code:

{% gist 0c9fa2f27a8992c2ad527947d0076e11 %}

maybe that will help someone. =) 