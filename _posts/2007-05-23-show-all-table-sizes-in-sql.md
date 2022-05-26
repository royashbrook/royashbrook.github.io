---
layout: post
title: show all table sizes in sql
---

this is a repost of something i wrote a long time ago. there are already stored procs to view table sizes on a specific table. this was just something that i wrote so that i could create a list of all of the tables in the current database and change the sort orders if i wanted. it also gave me the ability to filter based on table sizes if i needed to only see tables in a certain range.

{% gist d5276ac16723d4bbae3cab7c03c46887 %}