---
layout: post
title: stored procedure optimization
---

i get questions and emails like 'can you look at proc/sql/ddl x and make it better' all the time. so, i figured i would publish my answers or refactors here for public scrutiny. i think i've seen several blogs that do something similar like this. learning from your questions... ? whatever. here is one i got the other day

proc description:

update a statistics table called typetables. this table has stats about lookup type tables of some sort. one of the stats is a measurement of a two subtypes. i don't know, but i can only assume this information is being tracked so statistics about how heavily all of the type tables use which subtype can be reported on. anyway, this proc is very straightforward and it worked fine. i was just asked to look and see if i had any opinions on making it better.

{% gist 300f7a1b015330a2d8fd202ac56f3518 %}

my change explanation:

i added an index so we don't need the order by statement. this is really the key for this table anyway so it seems like a good idea. i also turned on nocount and set the isolation level to dirty reads since that should be fine for our purposes and will help with speed. specified a fast_forward cursor which should help with speed. i'm using the other looping style for cursors, but i don't really have a strong opinion about one or the other. i just hate putting two fetch statements. if anyone has an opinion on that feel free to share, i've never researched it i don't think. sql2005 uses statement level caching, so i seperated the select and the update statements and used sp_execute sql with a param. the update statement should stay cached as it won't change and the same plan should be used for all of the other executions of the cursor. i think we're kinda out of luck with the select statement since it uses different table names. if we were *just* using rowcounts we could use the sysindexes table for numbers as good as our last stat update, but we need a count of a specific id so i think this is probably our best bet. i think i could dynamically build one huge sql statement to join and count on everything, but that seems like overkill. opinions on that are welcome. i digress anyway. the result is a smaller simpler set of queries really. and we halve our executions since each loop only executes one update statement. i did break out the select as i'm not sure about subselect or inline query caching. i know statement level is supported, so the updates shouldn't take a hit every round because the table name is updated for the select, but if i put the numbers into an sub query in the update statement i'm not sure. any info on that would be appreciated. as it was, this seemed to run faster than the original query and i knew some of the optimizations should help so i just kicked it back as is. hope this helps someone else! enjoy.