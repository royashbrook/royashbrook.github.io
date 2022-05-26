---
layout: post
title: tables for layout... boo
---

this post is re: [this article](http://olav.dk/articles/tables.html).

the author makes tons of great points for reasons to use tables for ease of layout. the problem is that the point of not using tables is so that your content is not tied to your layout in any way. while i agree that for simple easy formatting, tables are FAR superior to css in many ways, the problem is that you probably will want to modify the formatting at some point. why not be able to completely customize your entire site with one file rather than modifying all of your files. if you are displaying tabular data, then of course you should use a table. but for page layout, i think you are missing the boat if you regress back to table layout.

css is not that much more complicated and really comes in handy if you need to go back later and modify everything. the examples he cites might be considered complex by some, but honestly how many times do you need to create those layouts from scratch? there are a million examples on the web on how to do that, just pick one. i have tried to work from the practice of ensuring a text based browser can read my content, and then apply beautification to the page and effects etc. content *SHOULD* be king, so you should be able to navigate your page regardless of whether you use tables or not.

as with many things, i would say it's always relative. obviously if you only have 15 minutes to write a quick app, maybe the IDE you are using will create tables and then you just leave them. but if you have any say so, i would always recommend going with css. 

the author comes to this conclusion:

> __A table is the right solution when we need one or more box to stretch beyond the natural (content-determined) size, depending on the size of sibling boxes.__

> CSS layout is powerful enough, though, that many seemingly grid-like layouts can be emulated using some amount of clever tricks and hacks. But these solutions - clever as they may be - is __difficult to create and maintain__, and __dont really give any benefit compared to the much more straightforward use of tables for layout.__


the bolding is my doing and reflects the areas i would disagree with. the first is when a table is the right solution. i would say a table is the right solution WHEN and ONLY WHEN you need to display TABULAR DATA. the layout of your site *should* provide some easy customization, which a table does not. saying that a css solution is difficult to create and maintain is .... well i think it's silly. if you have ever tried to modify the layout of a site with even 20 pages that is table based vs a site that is css based, you will see which one is truly difficult to create and maintain. css site templates abound on the web and i highly doubt you will find a table based layout that is far more elegant than a css based one. also saying that css layouts don't really give any benefit... is again.... just silly in my opinion. if anything using a table layout gives absolutely no benefit except the ability to quickly write something that should be seen the same on everything. which is great unless you want the ability to use css for mobile devices, screen readers, or... well anything. table layouts are inflexible even if they are quick to write. i think that anyone that has written or is writing web sites professionally should have enough experience and knowledge to find a css layout that simulates the table look that you want. picking on these layouts, however, i think misses the point of content being king.
