---
layout: post
published: true
title: Moving 15 million documents to the cloud
subtitle: >-
  Are we sure we don't like on-prem?
date: '2022-04-18'
---

Moving an image repository to the cloud is not quite what I would call turnkey. Starting up a new one is, I think, relatively simple. At the simplest you could just grab a SaaS solution that offers the capability that you want. At the most complex, you could analyze all the difference services that you want and build something yourself. Moving an existing one has an array of considerations, however. Mostly I think these can be broken down for many non-IT folks as:

1. Does it work as least as well as it did before?
2. Does it cost less than it did before?

I've worked on several similar projects in the past. I think most were actually bigger than what I'm doing now, but they were at bigger companies so there were lots of decisions that were already made. Things like which vendor to use or what hardware to use or whether or not the cloud could be used were normally matters of internal policy and not something the project team was able to change or address.

Currently, I am working at a relatively small company for myself. ~500 people and ~100MM in revenue. I'm not sure on the exact numbers, but just for sizing it's around there. We are a transportation company and everything transported has papers associated with it. We also have expenses and receipts that need to be tracked. I figured I would write about migrating this system to the cloud. 

### As-Is

Currently, we already have a new repository system in place. All new images go there and all that system works fine, but we did not want to move all of the old stuff over to keep costs down. The new system is not cheap in terms of storage and that's a discussion for another day. We had a team that was still adding some data to it while they migrated that pinned the old system in place until recently. Now they have moved to the new system, so we can address the old system in terms of what to do with it for a longer term home. I'll call this system the legacy system. The legacy system is used for reference and searching for older documents on an as needed basis. It's not frequently needed, but it *is* still needed. Currently we have about 16 million documents on an old server. Normally there is an id number that exists in another system that we would look up the image in this system, but there are about 30 other fields that are used to index the documents as well for searching.

The existing system has a little over 16 million files stored and about 133million indexed values. Some of these documents have multiple pages, but they are generally just single page. The document table shows that there are about 13.5 million documents and maybe 10-20k more pages than there are document objects. Seems this system may have been purged prior to my time here in the last 5 years. This makes some sense as there are documents going back to 2002 for some index data, even though I don't see any actual file references dated earlier than 2006. As best we can, we'd like to take everything if possible since this is just an archive anyway.

Current system is a win2k8 machine running an old version of sql that has been migrated probably since sql 6.5 or sql 7 a few times. This is one of the last machines closing out a datacenter migration project. All major systems have been moved for some time, but now it's time to handle a few infrastructure servers and this one so all of the equipment can be decommissioned. The old version of windows and sql limits a few tooling decisions and wizards, but for the most part we'll just be working around those.


### To-Be

Target is currently to get the index data into some location where we can setup a simple web application to search the existing index data. When searching for solutions that involved storing blob data and using an existing index, I didn't see much. I'm guessing because it would seem that this is fairly simple, I think. Copy the blob data somewhere. Copy the index somewhere. Make a little app to search the index and give a link to the blob data.

_note: When I say 'didn't see much' I meant in terms of moving into an azure/aws type of canned solution. Most of those solutions involved ingesting data and handling the indexing there. In my case, I already have the index, so just want to import the index data and reference the blobs somewhere._

This is kind of high level. As this system maybe has a dozen users who may search it, it doesn't not require anything incredibly robust, but we don't want something that is bad either. The main goal for target state is maintaining the search and getting rid of the old infrastructure.

To that end, the current plan is something like this:

1. Handle SQL data (Index)
  1. Create a new temporary sql VM in azure.
  2. Copy a .bak file and restore it.
  3. Use the new server to purge anything needed, then migrate to sql azure
2. Handle Files
  1. ~11 Million files have already been moved into a Azure Storage Container
  2. Move the remaining files (~2.5 Million)
3. Create new searcher app
4. Turn off old machine (equipment will decomm in another effort)

### What happened?

As the new system is already in place and I'll probably have to manually reconcile some delta for some reason, I imagine my plan will likely get tweaked a little while I'm doing the actual work. Especially since I have not decided on the final solution for how to index things. So here's what actually happened. Basically my notes on things as I went through the process.

#### Day 1

Initial day was spent analyzing current state and coming up with some of my initial plans. Getting access to the current server, examining the data that we had out there. Looking at some options for target state. Looking at what we had on Azure already for this. Even looking at possibly using some OCR solutions to perhaps auto index this. In the end. I went with the most simple approach of just trying to move the blobs and setup what was minimally required to replace the current solution.  It's a lot of documents and I really need to keep the work time down as low as possible as well as the cost so, let's get on with things!

Initial high level plan after this day:

- Stage Data
  - Copy SQL to Cloud
    - Temporarily created sql VM
      - Patch, etc, blah
    - Copy up the .bak file
    - Restore the file to db
  - Copy Files to Cloud
    - Robocopy to temp server (in progress)
    - Move into azure storage
- Build replacement
  - Prepare data if required
  - Build solution on top of data


#### Day 2

Create temporary sql server. I simply searched for sql and picked the most recent windows server with the latest version of sql. I called this server DB01

snip from the export template:

```
"hardwareProfile": {
    "vmSize": "Standard_DS12"
},
"storageProfile": {
    "imageReference": {
        "publisher": "microsoftsqlserver",
        "offer": "sql2019-ws2022",
        "sku": "sqldev-gen2",
        "version": "latest"
    },
```

As soon as it was up, there seemed to be some windows updates. So I did those and restarted. Then I went and grabbed the RDP file for download. I'm using a mac and have the microsoft rdp app to use for this.

![image](https://user-images.githubusercontent.com/7390156/159936739-ee042d8b-b6d0-4637-901d-8bd5fd583509.png)

I opened RDP with no issues and just start>run and typed in the UNC of the source server, authenticated, and dragged the file over to the data drive on the new server. The backup file was about 100GB, so I didn't bother with any other fancy copy commands with auto-resume or something as I expected it to work fine in relatively short order and it did. I think it maybe took ~15-45 minutes to copy or so. I moved on to some other items while that was going in the background so I didn't watch it too closely and it was done when I came back to it.

Once it was done copying, I restored it to sql. I simply had to change the target file locations to match the servers drives and no issues there. I think maybe this took 30-60 minutes. As above, I just peaked on it to see it was still going from time to time and that's about it.

The new SQL server had, by default, ~1TB data and log drives so plenty of room for my purposes. We have ~4 or 5TB total blob data, but there is only ~0.5TB left on the active server as everything else has already been archived to Azure previously. 

To copy the files, I simply used robocopy to mirror the source folder using something like this: `ROBOCOPY \\sourceserver\sourceshare\docfolder f:\docfolder /MIR /MT:128`.

##### Summary

Initially I forgot to put the MT flag and it ran all night and only copied about 5% of the data. So the next morning I remembered I should do that so stopped the command and ran it again with 128 threads. It uses 8 threads by default. Just doing that increased the speed by about 10x for the copy and it finished in about an hour or two the next morning I believe.

I also originally created my vm with some wrong config and had to wipe it and start over. Not a big deal, but just worth noting as it was part of the experience. =)

At the end of the day I had the files copying and the database restored to a sql server in azure.

Plan for the next day:

Initial high level plan for next steps:

- Move DB from temp server to Azure SQL
- Finish moving files to temp server
- Move files from temp server to blob storage
- Get started on moving data to Azure Tables (or.... whatever?)

#### Day 3

First thing was getting the right MT switch on robocopy and getting that copy going. This worked without an issue and I moved on to copying the files to blob storage. These files are for archive purposes, but for now we are just putting them into cool storage. I already have a storage account named 'cool' that we use for this purpose. It has all of the older data in it already. I believe when I moved it over several years ago, I used azcopy to do this. In modern times, I can just use the azure storage explorer tool. It uses azcopy under the hood (you can actually copy the command once you've started the process) so it just makes my life easy. These blobs are stored in a single folder, then in that folder there are folders numbered 000-999, then each of those folders has the same numbered folders, then under those folders are the files. For top level folders we only have numbers 000 through 017 total. I think the old system must use roughly one folder per year as we do have ~18 years of data, but I'm not certain. For our purposes, it doesnt' really matter. It just matters that we only have folders 015, 016, and 017 left on the server.

015 and 016 have actually previously been copied, but as that was several years ago, I went ahead and mirrored those up to db01 anyway, and then i'll just deal with duplicates when using azure storage explorer. There is an upload button in this tool:

![image](https://user-images.githubusercontent.com/7390156/159946274-ea7ef4d7-f985-4105-9631-dd6077d759f8.png)

So I just went into the root folder as this is where I have all of the numbered folders. Hit the upload button, and then picked the 015 folder first and uploaded it to the root. I picked the default values as these were fine for me. By default I already have this container set to cool. 015 processed and said only 1 new file. It pops up with a question on duplicates and I chose the option to basically not upload duplicates. Same process with 016 and there were no new files, then with 017 which had all new files.

See screenshgot below. `doclink` is the name of the storage container (the software we're archiving from is called doclink) and the F: path is from the db01 where I am running this process.

![image](https://user-images.githubusercontent.com/7390156/159947272-cd77ad1c-d200-485a-be27-23c3d0e3fb6f.png)

I then pulled statistics on these three folders to make sure the file count was the same as on db01. You can pull statistics by going into the folder and hitting the appropriate button. See screenshot:

![image](https://user-images.githubusercontent.com/7390156/159948261-4014ff8f-7be9-4ed5-8723-fa3458e8d39c.png)

And here are the statistics results:

![image](https://user-images.githubusercontent.com/7390156/159947762-f204515c-9571-499d-a242-a480e8c901f6.png)

And then I simply right clicked on each folder on db01 to get a count:

![image](https://user-images.githubusercontent.com/7390156/159948702-3437eb5f-ccba-4e36-8527-372a9756f296.png)

Everything matches, so yay, this part is done. Files are moved.

_note: Worth noting here that I did go back and do a bunch of spot checks to just make sure files were where I expected them to be. Additionally, when I was fiddling with UI prototypes and messing around, I would simply pull random values from the index data, and then hit the files randomly as well. This happened later, but I already knew I would be doing that, so I just moved on at this point. Just wanted to toss an editor's note in here that I *did* do a little more than just check the counts to test the data was there. =)_

On the original server, Folders 015 and 016 were moved to another location so they don't show up in the app any longer. Just in case there are some kind of versioning updates or anything, I just want to minimize that. When a file is not there, the app will toss an error with the file name and we simply go and retrieve that out of cool storage. This is already an existing practice so nothing that will cause any unforseen experiences. 017 is left as I'll probably go back later and take one last look to see if anyone snuck a document in somehow as we have not completely disconnected all scanners, just repointed to them to the new system.

While this was going on, I was also working on the database migration. This part was relatively straightforward as well, I simply opened up management studio on db01 and picked the deploy to azure task. We have moved a number of databases to azure sql over the years, and this is generally how I do this.

![image](https://user-images.githubusercontent.com/7390156/159950866-8c1b6b1b-d68b-45d9-8fe2-a690f7ed7452.png)

There were a number of logins and linked servers and other references that failed when I tried initially. This is kind of normal for older servers as these things may be busted and not working already, but not throwing any errors since the server may have light use or some other situation like ours. I just deleted all of these objects and then the process went off without an issue. This process did take quite some time, but finished by the evening without issue.

I also copied the original .bak file to the cool storage and set it to archive. There isn't a real reason to think that we'll need this original file, but just in case this will be much easier than trying to retrieve tapes as that is where the old servers backups went. Very minimal cost for a tremendous reduction in headache should we need it in the future since we would do the same thing and just copy it to a temp server and restore it for whatever reason.

Azure SQL was made premium for the transfer, and then it was reduced in size and the database was moved into our sql pool so we could give up that instance. This database is also going to be removed eventually, but this way I can work with sql for now and we can drop the temp sql server.

Plan for the next day:

1. kill db01
2. move data to azure table

#### Day 4

So first thing I did on Day 4 was start writing this blog post. I had already intended to keep a running narrative, but I realized I was on Day 4 and just had notes of what I was doing which isn't exactly what I wanted to do originally. So I caught up to here. =)

_note: The entire goal of this day-by-day log was to give some insight into this type of a process. So letting my notes languish seemed like a bad idea. =P fortunately I got it going at this point as I had some interruptions later that would have made recounting what all went on a bit of a nightmare._

I'm still not exactly sure what shape the data is going to take, so I cleared out all of the data from the database just to narrow the objects in my head space for now. DB01 was turned off for now and I am using Azure Data Studio for all of my sql work on my mac. The key tables with metadata that I need are these:

- PropertyCharValues
- PropertyDateValues
- PropertyFloatValues
- DocumentFiles

There are some other 'type' tables and also a table for mapping the file to the documentid as well as a handful of other items. But mainly we need the index data for the property value tables and which file it goes with. There are some versioning considerations also, but mainly my point is there are another 300+ tables that we don't need. So I first want to clean this out just so we can retain this base copy of all of the metadata, but skip all of the junk we don't. So next I went through all of the tables and other objects and cleaned things out.

First I wiped all of the views using `Select 'drop view [' + [name] + ']' From sys.objects where type = 'v'`. Note that this will actually catch some system views that won't delete. But I wasn't worried about this since this is basicaly a scratch type of db and worked for my purposes.

To do this I used variations on this sql:

```sql
SELECT
'drop table [' + t.NAME + ']' AS TableName,
MAX(p.rows) AS RowCounts,
(SUM(a.total_pages) * 8) / 1024.0 as TotalSpaceMB,
(SUM(a.used_pages) * 8) / 1024.0 as UsedSpaceMB,
(SUM(a.data_pages) * 8) /1024.0 as DataSpaceMB
FROM sys.tables t
INNER JOIN sys.indexes i ON t.OBJECT_ID = i.object_id
INNER JOIN sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
INNER JOIN sys.allocation_units a ON p.partition_id = a.container_id
WHERE i.OBJECT_ID > 255
and t.type = 'u'
AND i.index_id IN (0,1)
GROUP BY t.NAME
having MAX(p.rows) < 1
ORDER BY TotalSpaceMB DESC
```

which is a variation of the script found [here](https://blog.sqlauthority.com/2021/02/12/sql-server-list-tables-with-size-and-row-counts/?msclkid=42a165deab9911ec9ea61f00cf49541a). There are tons of ways to do this, but I just picked the first one from google for listing all table row counts.

I also used a variation of the view query above to sort some of the tables by names when i knew groups of names were not needed. `select * From sys.objects where type = 'u' and name like 'app%'` for example.

First I wiped all tables that had 0 records. One of those had a FK, so I checked it out and it was part of a group of tables that started with ERM, there appeared to be a group with that and ERT. I think these had to do with printing. I wiped these as well and dropped about 5GB and maybe 20 million rows total from the DB.

After this, I did a couple more batches of tables and every time got a handful of constraint issues. After dealing with a few manually, I hit up google for a 'wipe all fk constraints' script. Used this:

```sql
declare @sql nvarchar(max) = (
    select 
        'alter table ' + quotename(schema_name(schema_id)) + '.' +
        quotename(object_name(parent_object_id)) +
        ' drop constraint '+quotename(name) + ';'
    from sys.foreign_keys
    for xml path('')
);
exec sp_executesql @sql;
```

which was one of the solutions [here](https://stackoverflow.com/questions/12721823/how-to-drop-all-foreign-key-constraints-in-all-tables?msclkid=54954aa9aba411eca4db441e338e3170)

Dropping audit tables got me back another 10-15GB and 25million rows or so. I basically repeated this process several times over about an hour or so to just keep peeling out data. While I was doing this, I would pull out some kind of batch by name or some other criteria, maybe take a look through the tables to make sure there was nothing I wanted to keep, and then purge any other tables. After about an hour I had peeled ~25GB out of the ~100GB db. Obviously I hadn't made it to any of the big tables yet and *hopefully* we would end up keeping quite a bit as most of this data *is* index data, but you never know.

I had a soft target of at least getting below 50GB as I had to increase my pool DB max size from 50GB to fit this particular database, so I wanted to target that so I could put things back where they were. =)

Finally, I got everything down to 11 tables. Most were pretty small as they are just lookup tables so I wouldn't need those long term, but I'm going to hang onto them for the moment. Since there is no report helper in azure data studio, I had to look up a query to pull the same data.

query from [here](https://stackoverflow.com/questions/14685596/queries-to-generate-a-disk-usage-by-top-tables-report-in-sql-azure?msclkid=97bf34acabae11ec9366f59a613f23a1)
```sql
 SELECT TOP 1000
        a3.name AS SchemaName,
        a2.name AS TableName,
        a1.rows as Row_Count,
        (a1.reserved )* 8.0 / 1024 AS reserved_mb,
        a1.data * 8.0 / 1024 AS data_mb,
        (CASE WHEN (a1.used ) > a1.data THEN (a1.used ) - a1.data ELSE 0 END) * 8.0 / 1024 AS index_size_mb,
        (CASE WHEN (a1.reserved ) > a1.used THEN (a1.reserved ) - a1.used ELSE 0 END) * 8.0 / 1024 AS unused_mb

    FROM    (   SELECT
                ps.object_id,
                SUM ( CASE WHEN (ps.index_id < 2) THEN row_count    ELSE 0 END ) AS [rows],
                SUM (ps.reserved_page_count) AS reserved,
                SUM (CASE   WHEN (ps.index_id < 2) THEN (ps.in_row_data_page_count + ps.lob_used_page_count + ps.row_overflow_used_page_count)
                            ELSE (ps.lob_used_page_count + ps.row_overflow_used_page_count) END
                    ) AS data,
                SUM (ps.used_page_count) AS used
                FROM sys.dm_db_partition_stats ps
                GROUP BY ps.object_id
            ) AS a1

    INNER JOIN sys.all_objects a2  ON ( a1.object_id = a2.object_id )

    INNER JOIN sys.schemas a3 ON (a2.schema_id = a3.schema_id)

    WHERE a2.type <> N'S' and a2.type <> N'IT'   
    order by a1.data desc 
```

results:
![image](https://user-images.githubusercontent.com/7390156/160002173-fc69d3cb-d164-4c21-bf93-f6e8db9e5888.png)

Not too shabby, but I think we can probably kick some of these indexes off. Given the size difference, I started with the Documents and DocumentFiles tables to see how performance was dropping the non PK indexes. Most likely, this will all simply end up as a single table using the file name as the key since that file name matches the blob path to the file. As this is an archive, there will be no more property types or anything to add, so there isn't really a need to map types to tables,etc. We can just use columns. I'm thinking this will be easier/faster to do, in terms of manipulating the data, within SQL. But once I get a canned query, I may just extract the data a year at a time and import it.

It also *appears* that I could just add these values as tags onto the blobs and I think searching that would just be a traffic cost which is minimal as far as I can tell. Most of the pricing data I could find had to do with using azure search which would be more in tune with using AI to build an index and search based on extracting data. But since I already have the index, that doesn't seem really needed. These are just thoughts I'm thinking while I wait for the initial index drop to take place on the smaller tables. =)

...time passes...

After awhile, I killed the spid that was running the index dropping command. Not sure where it was hung up, but I ran multiple drop index commands. looks like it got through one table and was hung up on the other or just processing. I could see via sp_who2 it was doing disk io, but i didn't want to wait so I killed it. After this I simply disabled the indexes on that table and it cleared the space.

After this I decided to go ahead and size up the remaining indexes so I could target the big ones I didn't want

I used the script below from [here](https://basitaalishan.com/2012/07/06/find-the-size-of-index-in-sql-server/?msclkid=818831bcabbb11eca2845091910ba64e)

```sql
SELECT i.[name] AS IndexName
    ,SUM(s.[used_page_count]) * 8 AS IndexSizeKB
FROM sys.dm_db_partition_stats AS s
INNER JOIN sys.indexes AS i ON s.[object_id] = i.[object_id]
    AND s.[index_id] = i.[index_id]
GROUP BY i.[name]
ORDER BY SUM(s.[used_page_count]) desc
```

For our propertycharvalue table (has most of the metadata) there is a single index that is used which has our documentid, the value, and the propertyid. Since that's all we need, we'll disable/drop the other indexes.

...time passes...

After some more fiddling around, I finally noticed that the indexes that were named as if they were clustered, were not in fact clustered. this was causing some inadvertant index disabling and just generally silliness. So, make sure you know which are clustered and which aren't before you start messing around. It just so happened that the first index I picked to disable on several of the tables happened to be the clustered index even though the name made me think it was not. Lesson learned. =) Unfortunately, disabling that index meant we needed to rebuild it to turn it back on. This took some time. =)

...later that night...

Rebuilding the main index took ~4 hours. Once this was finished I removed all of the indexes that weren't the PK or clustered. This brought our space used down to ~13GB. That's more in line with where I was wanting to get to. I really only think we need about half of that as it is at most, but mostly was just wanting to squish down so i could reduce the pool size to normal.

A quick google shows that dbcc is still [the way](https://docs.microsoft.com/en-us/azure/azure-sql/database/file-space-manage#shrink-large-databases) to go so I checked the files (only one for this db) and ran a shrink with a target of 30GB. This took about 2 hours, but when done I was able to resize the pool and that ticks the box for me.

Plan for the next day is continued. Since it's very late, I'll probably change it some tomorrow based on what went on today. =)


#### Day 5

Data transforming hell. =) I am leaning more towards putting this database into cosmos db at this point, but I don't want to have 100 million objects with empty properties as many of these don't have the same properties, so I need to clean things up a bit more and squish down a bit more. This, unfortunately, takes a lot of time with this many rows. After some manipulation, our final table has just over 15 million documents and about 10 properties per document on average.

Due to the way I was working on this, I did end up spinning back up the DB server so I could work on the pool as well as doing some additional work on the VM because they wouldn't share resources. I didn't run into anything that seemed to be performance bottleneck'ed on the pool, but since i had another box I just spun it up since they don't touch each other at all.

I'm not 100% sure on how to convert this to json and do it with this many documents and have the performance reasonable quite yet... time will tell I suppose.

#### Day 6

It is now a Saturday, so I could just put this on the shelf, but I have a hard time letting go of tasks that may need to run in batch over a long period of time when there is a weekend. So I took some more time to work on this. A big part of what I was thinking about was whether I wanted to go nosql with this dataset. I was leaning in this direction just due to the sporadic use of this data and the fact that nosql was kind of in vogue to a degree.

The dataset itself has been boiled down to a single table of index data for the files. FileID, Key, Value is basically the setup. Imaginatively using names of id,k,v. =) This table has 145million rows and takes up about 7GB. So, nothing crazy, but lots of rows. It is simply enough to just add search terms and filter things and this works, but is not particularly satisfying.

some sample sql where I call the table t0:
```sql
select * from t0 where id in (
select id from t0 where
(k = 2 and v like '123%')
and (k=2 and v = '1230')
and (k=2 and cast(v as int) > 1000)
--add more criteria as needed
)
```

In theory, there will be a form where there are some search boxes with different k ids (using friendly names) which is what the users experience in the old system. I just feel like I'd rather this was in a nosql setup where we just store the json objects. Ultimately, if I pull this data through a service, it will end up in json so I feel like I could cut out the middleman by just storing it and indexing it that way. This is a pretty simple use-case, but one of the issues then is naming the properties which I'd also like to do. Right now it's just integers for the key names since they reference pk values in the old system. But the benefit of this is that the property names can be called anything, but we sacrifice the stored object readability. I have been thinking about this all week and I think the risk of wanting to rename the property name, and then having to update 15 million objects seems like too great of a risk since the property name can just stay as part of the UI. I am not in love with self describing data as a rule, but in this case I kind of feel like it would be nice.

Another benefit (to me) of moving to cosmos (or some other nosql) is that I can just query that directly. Otherwise I'll need some service to get the sql data for the SPA. Using nosql I believe I can just go client first once all the auth is done with azuread/o365.

I decided I wanted to convert the data to json at a minimum. Due to the amount of rows, I was looking for a quick way to eliminate all of the properties that didn't exist for individual items. This is kind of turnkey with a table holding key value pairs for some id, but since I wasn't sure which properties were missing, the easiest way seemed to be to create a giant pivot table with all of the columns, and then I could use for xml auto to eliminate all of the nulls automatically. So to that end, I ran something like this:

```sql
select
	t.id
	,[1] = (select top 1 v from t0 tt where tt.id = t.id and k=1)
	,[2] = (select top 1 v from t0 tt where tt.id = t.id and k=2)
	,[3] = (select top 1 v from t0 tt where tt.id = t.id and k=3)
	--... etc for about 50 different rows
  -- I generated these values with this
  --   select ',['+kt+'] = (select top 1 v from t0 tt where tt.id = t.id and k='+kt+')' from
  --   (select distinct k,[kt]=cast(k as varchar(2)) from t0) x order by k
into
	t0w --i labeled this t0 'wide' in my head
from
	t0 t
group by
	t.id

```

I feel like there is a better way to do this looking at some of the newer agg and json functions for sql, but I only need to run this once so after searching around and messing around partially the last couple of days looking at that stuff while doing other things, I opted to just do this. There are some functions in the data factory that also seem to be specifically built for somet things like this, but, again, this is one time so... seems like overkill when I can just fire this off and come back to it later.

After this table is done, I can create a table that holds all of the json objects like:

```sql
select * into t0json from t0w for json auto
```

You may wonder why create an additional tables since this is a one off. But while I was working on this, I sometimes wanted to test something and have several different next steps or just examine the data in stages. It was way easier to just put it into next step tables so I could come back to it later.

...time passes while insert runs...

I should say this was the initial insert for the normalized table. This table was actually pretty small. i did insert this into a second table using this sql to just get the json:

```sql
--took about 7 min
select o=(select * from t0w aa where aa.id = a.id for json auto) into t0wj from t0w a
```

and these were the sizes:

![image](https://user-images.githubusercontent.com/7390156/160253990-34e155e1-83b2-4926-807a-f4d7db149b6a.png)

I realize it's not a huge difference, but iw as surprised that the id,k,v table was so much larger than the normalized table. Not terribly shocked that the json table was much bigger just because lots of kind of redundant text. but those null values don't even exist in the base table so was kind of surprised. i'm ignoring the index really and just talking about the data size here currently. 

I also noticed after i was done that I should add the `, WITHOUT_ARRAY_WRAPPER` option as well, although that didn't reduce the size much. ~60MB on the t0wj table.

I decided to import this just using a file. I pulled the Azure Cosmos DB Data Migration Tool for this.

<img width="470" alt="image" src="https://user-images.githubusercontent.com/7390156/160256103-0ee1a279-7e0b-4dc1-b2b7-adda7aa0c48f.png">

Interesting. I did check the prettify and compress... maybe this number has to do with that? Let's see what's going on if I import this. =)

<img width="443" alt="image" src="https://user-images.githubusercontent.com/7390156/160260128-5721024e-11f1-4f22-b307-e75286485c07.png">

Took a bit longer, but still nothing crazy. =) This seemed great, except it appears that each record was imported as a single property of the object. So instead of something like this:

```json
{
	"id":"myid01",
	"1":"val1",
	"2":"val2"
}
```

we ended up with something like this:

```json
{
	"id": "edfeb95b-5162-4a35-8a62-dd6838cf1dc6",
	"JSON_F52E2B61-18A1-11d1-B105-00805F49916B": "[{\"id\":\"myid01\",\"1\":\"val1\",\"2\":\"val2\"}]
}
```

I was just running this while I was doing other stuff around the house on the weekend, so apparently whatever I clicked on didn't do what I expected. Time to try again it seems. =P

So this time I looked under advanced and updated some items differently. I also used the table that already had the pregenerated json data. The first time I did a for json auto, basically the same way I built the first query.

<img width="534" alt="image" src="https://user-images.githubusercontent.com/7390156/160260459-782f8788-ff68-4979-ae86-0465c8d56c3a.png">

...time passes while i fiddle with things...

After several iterations fiddling around with this, I thought I needed a clean start. What seemed to happen is the data migration tool would let me export and have null values, or if i exported and turned it into json on the way out, it would then embed it again into another json structure like above.

I had originally discarded the idea of partitioning the data by key, although that would be the best way to split things out, i believe. The idea, in my mind, was that i didn't think the cost would matter much and i don't really need to partition to scale just due to the nature of this app. So i felt that doing a slightly more costly search, but getting back all of the data in one shot would be better. After fiddling with things a bit, I was seeing that it might be a little smoother to just import all of my data and partition by the key name so there would be key partitions with an id and a value in it. Then to see all of the meta data, would have to go back and query for the id across all of the partitions, but maybe that's fine. In my case, the use is so light it didn't seem to really matter much.

I could also just write some powershell or something to extract the records and then dump each one into a json file that already looked like I wanted and then import it. When I did my initial test, I did something like that and I just imported that one small file, but after restructing the data a few times while testing, I wasn't really at that place anymore.

While working on this, I noticed that one of the text fields had dates in it that were formatted like MM/dd/yyyy. These / characters were all being escaped in the json and there was a date value previously in use that had date values in another table. I converted those all to yyyy-MM-dd when importing this, so I decided I should probably clean those values up just to get things consistent.

To, hopefully, make this a little quicker I did a quick check on what k values seemed to match these dates `select k, count(*) from t0 where v like '%/%/%' group by k` then took a look at the values to make sure they were dates by doing something like this for each of the k values that popped up: `select distinct v from t0 where k = 2 and v like '%/%/%'`. Some of these things weren't dates, just values with slashes in the value. Some of them were dates, but they had the date/time in them but the times were all midnight times. I'm guessing this is just based on some indexing that changed in the past. The times aren't needed since the users always only search by dates and I think they maybe can't even find these unless they know the text format to use for the search since these values were actually stored as text. Not sure why, I'm guessing it just worked out that way, so we can make it consistent now.

I updated all of these date values like so:

```sql
update t0
set v = format(cast(v as date),'yyyy-MM-dd')
where k in (1,2,3) and isdate(v) = 1 and v like '%/%/%'
--note that k values were set to whatever value i wanted to update at the time
```

... several weeks pass...

#### Day 7

After some other priorities jumped the line, I was able to make it back to this project. *UNNNNNNNN*fortunately it seems that my server got deallocated and I ran into some odd issues turning back on the azure vm. There were a couple, but mostly they seemed to really be something like this:

```
rashbrook@Azure:~$ az vm start --resource-group Servers --name db01 --subscription myid
(BadGatewayConnection) The network connectivity issue encountered for 'Microsoft.Compute'; cannot fulfill the request.
Code: BadGatewayConnection
Message: The network connectivity issue encountered for 'Microsoft.Compute'; cannot fulfill the request.
```

This server is in a.... somewhat unusual networking setup, so I think this could have something to do with this as there were some other changes in place. After fiddling with this in the background for about a half a day, I decided it really didn't matter because it's probably worth it to me to go back and review things and reset. There are some other fields that I think I will integrate anyway after some side conversations with some of the users over the last couple of weeks anyway.

To this end, I decided to, instead just load this stuff up on a docker instance so I could just work on this locally. I haven't re-installed docker since switching over to a mac, so... two birds. =)

First I installed docker by following the directions on the website, then headed to the MS page to get latest instructions on running a local sql:
https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&preserve-view=true&pivots=cs1-bash

Note I'm using \` instead of \\ below because I happened to run this in pwsh on the mac. I grabbed a random password from the lastpass site here: https://www.lastpass.com/features/password-generator but you could just make one up.

```
PS /Users/roy/gh/d> sudo docker pull mcr.microsoft.com/mssql/server:2019-latest
Password:
2019-latest: Pulling from mssql/server
ea362f368469: Pull complete 
dc034f624aa1: Pull complete 
cafda714f10f: Pull complete 
c6af4ce68233: Pull complete 
2e5e63d166b4: Pull complete 
Digest: sha256:fb5277e7a3cc53f7d2230ed089ed60849f79567ebb0aae8f41ceb85879e9e09d
Status: Downloaded newer image for mcr.microsoft.com/mssql/server:2019-latest
mcr.microsoft.com/mssql/server:2019-latest
PS /Users/roy/gh/d> sudo docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=NdFe!Vtb" `
>>    -p 1433:1433 --name sql1 --hostname sql1 `
>>    -d mcr.microsoft.com/mssql/server:2019-latest
WARNING: The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested
06a71cabea6fc1ef3fe0df294dde302ab2dd9da8873a38996a8e111231d5adad
PS /Users/roy/gh/d> 
```

ok.. so that makes sense. =) a google search later and I have this page:
https://database.guide/how-to-install-sql-server-on-an-m1-mac-arm64/

```
PS /Users/roy/gh/d> docker pull mcr.microsoft.com/azure-sql-edge
Using default tag: latest
latest: Pulling from azure-sql-edge
976e4515cbe3: Pull complete 
1f73897c23c8: Pull complete 
b4ff7ff6a52b: Pull complete 
b66501766227: Pull complete 
9fd306fd7e2d: Pull complete 
129b56f5ef99: Pull complete 
56de8af9a702: Pull complete 
3cce2408ea6b: Pull complete 
48dbc293ce87: Pull complete 
c04c7c72b2fa: Pull complete 
3ca071310af7: Pull complete 
Digest: sha256:7c203ad8b240ef3bff81ca9794f31936c9b864cc165dd187c23c5bfe06cf0340
Status: Downloaded newer image for mcr.microsoft.com/azure-sql-edge:latest
mcr.microsoft.com/azure-sql-edge:latest
PS /Users/roy/gh/d> sudo docker run -e "ACCEPT_EULA=1" -e "MSSQL_SA_PASSWORD=NdFe!Vtb" `
>> -p 1433:1433 --name sqledge --hostname sqledge `
>> -d mcr.microsoft.com/azure-sql-edge
Password:
c891e6d22bfb1a5011cb64053bfb8b39f538b543aea9bc6d31394bfaf87612b0
PS /Users/roy/gh/d> docker ps
CONTAINER ID   IMAGE                              COMMAND                  CREATED         STATUS         PORTS                              NAMES
c891e6d22bfb   mcr.microsoft.com/azure-sql-edge   "/opt/mssql/bin/perm…"   4 seconds ago   Up 3 seconds   1401/tcp, 0.0.0.0:1433->1433/tcp   sqledge
PS /Users/roy/gh/d> 
```

So that seemed to work. Azure data studio connecting to server `.` with sa and the password given gets us hooked up. Nice.

While I was doing that, I also jumped on the main server as I had decided I would retool my export to include some of the root properties of the document record itself. The way the data is stored in the system is there is a 'document' and the document has a file. The document could be updated, tied to work flow, and have dynamic properties assigned to it by type, etc, in the old system. In our case, we just need to retain the properties of this document along with the file name so we can locate the blob if we need to later. To do this, I created a unifying query that would simply place all of the properties into a simple table with the columsn of id, k, v for the document id, the property key (a property id), and a value.

I'd love to convert this right into a json object, I think, but unfortunately the original system is sql 2008 and doesn't have for json. So I'm just going to convert it as noted and export a csv file so I can get what I need off the box. I'm also going to do this by year as I can then move onto the UI and come back and do the export/conversion later for older years. SQL I used below:

```sql
-- issue/honor no locks and be the deadlock victim if needed
set transaction isolation level read uncommitted;
set deadlock_priority -10;
set nocount on;

;with d as (
	select
		  [id] = DocumentId, Created, Modified, DocumentTypeId, [PageCount]
	from Documents where YEAR(created) >= 2020
)
--new property id for created as utc
, p70 as (
	select
		  [id]
		, [k]  = 70
		, [v]  = cast(convert(varchar(19),DATEADD(minute, DATEDIFF(minute,getutcdate(),getdate()), [Created]),127) as varchar(50))
	from d
)
--new property id for modified as utc
, p71 as (
	select
		  [id]
		, [k]  = 71
		, [v]  = cast(convert(varchar(19),DATEADD(minute, DATEDIFF(minute,getutcdate(),getdate()), [Modified]),127) as varchar(50))
	from d
)
--new property id for DocumentTypeId 
, p72 as (
	select
		  [id]
		, [k]  = 72
		, [v]  = cast(DocumentTypeId as varchar(50))
	from d
)
--new property id for [PageCount] 
, p73 as (
	select
		  [id]
		, [k]  = 73
		, [v]  = cast([PageCount] as varchar(50))
	from d
)
--new property id for DocumentStoreFileID, aka the file name 
, p74 as (
	select
		  [id]
		, [k]  = 74
		, [v]  = cast(DocumentStoreFileID as varchar(50))
	from d join DocumentFiles v on v.ParentId = d.id
)
, pdv as (
	select
		  [id]
		, [k]  = PropertyID
		, [v]  = cast(convert(varchar(19),DATEADD(minute, DATEDIFF(minute,getutcdate(),getdate()), PropertyDateValue),127) as varchar(50))
	from d join PropertyDateValues v on v.ParentId = d.id
)
, pfv as (
	select
		  [id]
		, [k]  = PropertyID
		, [v]  = cast(PropertyFloatValue as varchar(50))
	from d join PropertyFloatValues v on v.ParentId = d.id
)
, pcv as (
	select
		  [id]
		, [k]  = PropertyID
		, [v]  = cast(PropertyCharValue as varchar(50))
	from d join PropertyCharValues v on v.ParentId = d.id
)
, m as (
select * from p70 union all
select * from p71 union all
select * from p72 union all
select * from p73 union all
select * from p74 union all
select * from pdv union all
select * from pfv union all
select * from pcv
)
select * from m
order by id,k
```

We stopped putting most documents in this system in mid 2019, so there are much fewer items to extract from 2020 onward. I went ahead and did some extracts for some additional years as well, below is a screenshot of the 2018 year which is more 'normal' in size.

![image](https://user-images.githubusercontent.com/7390156/162529060-838e3d5f-6e14-4308-92cd-c4957119e19d.png)

I simply used the import/export wizard for this. Basically standard flat file export, select csv for export, named the csv with the year, put `"` as the text qualifier, and check the 'columns in first row' box. Not the fastest thing, but I really only needed the first one and just ran the years prior to 2020 to see the export sizes. But now that they're done so.... I mean may as well use them. I had planned to do some other cleanup or something in SQL, but now I have these flat files, and they only took about 2 min or so to export a piece per year.... I think I can just import these as azure tables and..... maybe that's the way to go just to make my life easy haha. I think cosmos has a slightly better model, but there are no more than 10 people probably that will ever search this so.... it likely doesn't matter at all. I'd rather have a document database with 15 million objects than a 3 column table with just the key value pairs that has 150 million rows, I think, but hard to know which is best without testing.

All the info I can find online seems to be more geared towards a more holistic solution vs just archiving and searching the meta data, so I'll play it by ear for the moment. I could also just dump it into a sql db and write a very traditional app of any flavor to search the one table.

I had to get on a phone call, so while I was on there I went ahead and exported the data for the previous 10 years which is all that anyone really *wanted* to keep, although we did have data going back another 5 years. I believe there was a previous purge of the existing system based on some missing files, so some of that old index data is no good anyway. I'll just stick with this for now. We do have backups of the entire system in the event there is some emergency and we need to go and grab a full restore. Each *full* year was about 450MB of csv data with the prior 3 years taking up only ~20MB total.

Unfortunately, this server is old enough that it seems the azure portal doesn't work via the web and I can't update the browser anymore. Understandable. =) So I had to copy the files down to my local machine next. I am interested in seeing what type of memory consumption doing a powershell read on one of these 500mb csv files will produce. Copying the 4GB files down over VPN took about 30 min. There is a lot of waiting on things to finish with this sort of thing. =P

...time passes while I import data...

#### Day 8

After importing data for a day or so, all the data was in cosmos. This was just checking every couple of hours or so tops and picking a new file. I only had 10 years to do, so this was just some time spent popping onto my computer over the weekend. Unfortunately, the data for azure tables is just way too slow on the import. So I'm going to nix that for now. This day was the final imports and also my db01 server returned to service, so I hopped on there to see if migrating from a cloud csv into azure tables was going to be faster. Sadly, I was already done with the 2020s and 2019 and the next year back was ~20x the size. Since this was indicative, anyway, of the rest of the years to import, I tried doing that on the azure sql box using the azure storage tool. Long story short, it just would not ever open in that tool, so I couldn't start the import. Since it took _days_ to do the tiniest year from my computer outside of the azure network, I just decided to drop this. I did try quite a few times and rebooted and patched and tried other various things, but the tool just could not seem to handle the csv file which was 400MB in size. There were only 3 columns an id, a property id, and a value. I basically used the id as the PartitionKey, the propertyid as the RowKey, and then the value was set per row. This worked fine with the smaller years, but it would not even open the larger files to show the preview.

Maybe there was a way to do this without showing a preview, but as I was already completely finished with all 10+current years import for cosmos, I just tabled this for now and moved on to my next step which was importing a copy of the id,k,v structure into azure sql. I still have room on the pool for storage under the current plan, so this wasn't going to cost anything extra and it took up about 8GB of space. Theh cosmos db took up just under 10GB of space, so not a major storage size difference moving to cosmos. I suppose if the data was 100x bigger, maybe that would make a difference. Fortunately for this solution, I think this will be fine probably forever. Considering this is 10 years of data.

I also stopped by local sql docker instance as I wasn't using it. Doesn't matter much, but it was a loose plot thread here I thought I'd tie up =P The reason for this was just that since db01 popped back up finally, I just did the data manipulation there and I already had wanted to test the import from that box since it was already *in* Azure. That box was deallocated, and I think that on startup I was just getting not helpful errors all along the way while it was spinning up. I didn't do anything other than start it and then watch it fail over and over when I was looking at it. But when I checked the next day it was back up and everything was working. I didn't go back and look at any of the job details to find more info as I'm guessing it was just something with the spin up related to not waiting and ultimately I moved on anyway. Anyway, since it was up I could do the little bit of testing and just do the sql manipulation I wanted to do on that box before deleting it instead of doing it locally. Glad I did get docker back installed for next time I need to do something similar.

#### Part 1, done. Thoughts.

My goal for this post was simply to provide an account of someone actually doing this process. Hopefully this gives some insight to someone else, or at least future me about what happened here. =)

My original high level to do:

1. Handle SQL data (Index)
2. Handle Files
3. Create new searcher app
4. Turn off old machine (equipment will decomm in another effort)

I found that as I was tracking this work for myself, I kind of got distracted by #3 while I was in the midst of #1 and #2. In part, I think this was largely because I thought I wanted to move to cosmos for the database access, but wanted to 'test' the azure tables approach which led to a lot of kind of back and forth mentally for me. Also, there was a lot of 'waiting' during imports that led to idle thoughts about #3 which turned into actual coding. =)

I also had several unrelated projects jump into the middle of this that I had to handle that did not really help that back and forth. Ultimately, I pulled the tricker on cosmos, and then imported the same CSV data into azure sql as a backup if I needed to do some adhoc query of the data and didn't want to do it on cosmos for some reason.

After solidifying that, Step 1 and 2 were done, and knowing Step 4 is not something I'm going to write about at all, I was left with Step 3. I moved on to doing that already, but realized I should probably cut this entry here as the problems/topics for that are completely different. Ultimately I expect that Step to boil down to 'how do i want to wrap and interact with this data in a way that makes sense to the existing users?'

I think that, as with much prototyping, this process would have been faster if I had focused on 'speed'. I could have simply grabbed the 2020s data which was tiny and then started prototyping a UI. But since I already had all of the data, I instead focused on getting all of the data out there so I didn't have to address that step again. Some of this is just due to the nature of the way work flows for me currently. I may have a project bump out all current work due to a business need at any time and sometimes I have to shelve work for months. So I am always in a state of thinking about how I may need to shelve something tomorrow and try not to get things 'halfway' setup that I can't easily delete. Creating a prototype that could be orphaned for a long time isn't valuable, but getting this data 'migrated' is valuable because we *could* lose or shut down the original machine in a pinch if this project is shelved for a year because at that time this archive will be even older and have even less frequent use.

That being said, if I could go back, I would rather have (from a dev standpoint) just dumped a couple years of data somewhere, prototyped away, and then wrapped things up with a '...now i just need to import the rest of the data....' type of idea. But had I used azure tables for that, I may have been sad in the future when I tried to upload all of that data though. =P

Regardless, I am declaring this part 'done' and moving on. Done > Perfect, and I am done moving the files and the index data to azure.

Next up, the indexing app. =)

PS: I can't remember if I noted it up above, but also adding backups on all of these items in Azure is included. I am just doing 24 hour backups on cosmos (as we don't change this data ever) and maintaining the default daily type of backup policy on the sql pool for that copy. As mentioned, there is a copy of the original .bak file with the blob storage in archive mode for 'just in case one day' scenario and the blobs have soft deletes turned on. We also do have some tape backups of the data from past years offsite that we may maintain forever. It's unlikely those will come into play, but it will provide some reasonable assurance *if* we ever want to purge documents that are very old. Although blob storage is so cheap, it seems like better to just move everything that is super old into 'archive' status then just add a step in the logic to get the document that requires a ticket instead of allowing the user to get super old documents.

Edit: I noticed later when I went back to re-import things that I did not list the powershell script I used to export the data to json files for import into cosmos. Adding that below. Originally I did this on a sql vm on azure that doesn't exist, but on my re-import I used a docker sql image, so you'd need to adjust accordingly. Below also assumes that you have a table with the id,k,v fields in place for export. Below I'm showing the dump of index data from 2010 and I am using the id value stored in the database for the id on the object instead of generating a guid on import.

```
# get data from sql
Import-Module SqlServer -Cmdlet "Invoke-Sqlcmd"
$cs = "Server=.;database=fuzzy;User ID=sa;Password=NdFe!Vtb;"
$q = "select * from _2010 where exists (select 1 from documents where documentid = id and year(created)=2010)"
$dt = Invoke-Sqlcmd -ConnectionString $cs -Query ($q)

# group sql data on id
$xg = $dt |
  Select-Object $dt.Columns.ColumnName |
  Group-Object id

# convert to hash table
$ht = foreach($g in $xg){
  $values = @{}
  $values["id"] = $g.name
  foreach ($gv in $g.Group) {
    $values["$($gv.k)"] = $gv.v
  }
  $values
}

#convert hash table to json file
$ht |
  ConvertTo-Json -Depth 4 -Compress |
  Set-Content .\2010.json
```
