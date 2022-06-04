---
layout: post
title: more cool usage information for sql2005
---

yet [another incredible article from chad boyd's blog](http://blogs.msdn.com/chadboyd/archive/2007/02/02/sys-dm-os-buffer-descriptors-aggregations.aspx)

this article is about "determining what objects/structures/files/etc. are consuming the largest amount of space at a given time (or over time) within the Sql Server buffer pool" in sql 2005.

i'm going to post something similar to the little one here. I had a little trouble with it running out of space in int for bytes, so I just switched to KB since that's plenty precise for me. I also switched the formatting to something more consistent with what I typically use. code below, enjoy!

{% gist 6d597487d04affd8c29132d992fa2822 %}