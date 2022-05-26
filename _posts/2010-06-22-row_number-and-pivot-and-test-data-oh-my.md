---
layout: post
title: ROW_NUMBER() AND PIVOT AND TEST DATA, OH MY!
---

So the other day I got the following question about reporting on some log data:

> The data being loaded is a bunch of machine log files. (E.g. MachineName Status Timestamp) The status is a simple Start or Stop. My question is can I use Datediff between sequential records to determine elapsed time between start and stop? I know this would work if the start and stop data were in the same record and I believe this will work between sequential records but I'm fuzzy on what's best and putting it into action

Below is the end result of my answer. There are three basic parts of the solution below:

1. Build some test data and stage it
2. Group the data and make some keys to re-join it
3. Write a little report to show what we are after
4. (bonus) a little pivot table to show some other groupings on the data =D

{% gist b98af287a02f9431e363673f26d9d3cb %}
