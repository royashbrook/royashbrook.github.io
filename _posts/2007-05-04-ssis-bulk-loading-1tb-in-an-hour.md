---
layout: post
title: SSIS, Bulk Loading, 1TB in an hour??
---

OK, so several times recently I have heard about SSIS being able to load 1TB in an hour. These comments had no reference other than something they read once, which is cool because we all probably do that stuff all the time. So, I have current need to research and define some SSIS and General Integration best practices. One of the items I came across was ["Load 1TB in less than 1 hour"](http://blogs.msdn.com/sqlcat/archive/2006/05/19/602142.aspx) which is quite sweet. By quite sweet, I mean that it does show you to load a massive amount of data. It is also sweet in that this guy has access to some super sweet hardware. Here's a quote:
 
> This project was done using SQL Server 2005 Enterprise Edition (with SP1 beta) using the BULK INSERT command with 60 parallel input files, each of approximately 16.67 GB.  The best time I got was 50 minutes.  Hewlett Packard was kind enough to loan me some time one night on the machine they use to do the TPC-C tests.  It is an Itanium2 Superdome with 64 1.6GHZ 9M  (Madison) CPUs.  For the insert tests the system was equipped with 256GB of RAM. The HP SAN configuration is rated at 14GB per second throughput.
 
Whoa! Now that is some fat hardware. I'm surprised no one is using some incredibly large solid state drive to make all of this look even sillier. Maybe those things are solid state at 14GB per second. I dunno. Anyway, there are definitely some cool things in there and you should check it out. In particular I noted the batch size. Here is a pertinent quote. I will simply snip the 'lessons learned' section which contains the reference to the batch size:
 
> BEST PRACTICES and LESSIONS LEARNED

> Here is a list of things we learned during this project that should be helpful in your projects.

> - Run as many load processes as you have available CPUs.  If you have 32 CPUs, run 32 parallel loads.  If you have 8 CPUs, run 8 parallel loads. 
> - If you have control over the creation of your input files, make them of a size that is evenly divisible by the number of load threads you want to run in parallel.  Also make sure all records belong to one partition if you want to use the switch partition strategy.
> - Use BULK insert instead of BCP if you are running the process on the SQL Server machine. 
> - Use table partitioning to gain another 8-10%, but only if your input files are GUARANTEED to match your partitioning function, meaning that all records in one file must be in the same partition.
> - Use TABLOCK to avoid row at a time locking.
> - Use ROWS PER BATCH = 2500, or something near this if you are importing multiple streams into one table.

Explanation from the comments:

> 2500 rows per batch is used to avoid lock escalation 

Interesting. Lots of fun reading so let's move on. One of the other cool articles I came across was "More on Flat File Bulk Import methods speed comparison in SQL Server 2005" which basically taught me that BULK INSERT is really the way to go man! SSIS with Fastparse is pretty cool too, but for adhoc loading, BULK INSERT is quick, easy to setup and wins handily in many tests. Now, all tests aren't created equal so if you have a repeatable process, the 'test, test and test again' methodology definitely still applies. Here's a snip of the results, but you will definitely want to read the full article for details on the testing setup and methods.

Method|Time (seconds)|CPU utilization (%)|RAM change (MB)
--- | --- | --- | ---
Bulk insert with Tablock |4648|75|+5MB
SSIS FastParse	|5812	|75	|+30Mb
BCP with Tablock	|6300	|100	|+5MB
Bulk insert	|7812	|75	|+5MB
OpenRowset	|8750	|95	|+5MB
BCP	|11250	|100	|+5MB

For those of you not familiar with the BULK INSERT command, please consult SQL BOL for information. Here's a sample command to output a fmt file:

` bcp AdventureWorks.HumanResources.Department format nul -T -n -f Department-n.fmt `

[Here](http://msdn2.microsoft.com/en-us/library/ms191516.aspx) is a link to an msdn article talking about fmt files. Please check out the linked material for more information.

Here is some sample BULK INSERT code:

``` SQL
bulk insert my_table
from 'c:\my_table_values.txt'
with
(
      formatfile = 'c:\my_table.fmt'
      , tablock
)
```
 
Here's another MSDN article about bulk operation type stuff:
http://msdn2.microsoft.com/en-us/library/ms187042.aspx

I don't have a need to do much power sorting with my imports now, but if I ever did these guys claim to be really fast!

Lastly, check out [this cool strategy](http://blogs.conchango.com/jamiethomson/archive/2006/12/12/SSIS_3A00_-Dropping-data-into-a-raw-file.aspx) for staging data using raw files. I've never tried it but it sure sounds cool and I like all the reasons for using it. 

I suppose as a PS, I will include this link to a comparison of 64-gigabyte solid-state drive (SSD) to a serial ATA (SATA) array. To summarize, it's *way* freaking faster. =) I think for the money, small shops with heavily DB dependent applications can do nothing easier and faster to improve response time then add a ton of memory and switch over to SSD. If you are having response times, eliminating that bottleneck seems like it will always pay dividends. 'Disks that go fast' is loosely related to bulk loading so cut me some slack on including this here. =)