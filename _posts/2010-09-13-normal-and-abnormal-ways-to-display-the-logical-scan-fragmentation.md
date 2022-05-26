---
layout: post
title: Normal and abnormal ways to display the logical scan fragmentation in sql database
---

So the way I am getting this data is just normal dbcc showcontig in a db. But for some reason, when I saw the output of dbcc showcontig today, I just decided I *had* to parse it with the command line. /sigh I just wanted the logical scan fragmentation. So this is, in part, the output.

```plaintext
Profile_Lookup 99

MSSCrawledProperties 96

MSSManagedProperties 66

UserProfileEventLog 50

UserColleagues 40

UserMemberships 85

UserSites 66

MembershipRecursive 73

MembershipNonRecursive 1
```

Etc etc etc etc

 

To do this I just did a simple for loop with a call out to a …. I forget what these things are even called since no one uses them anymore or cares. I thought about trying to convert it to one command (that's what I started with) but there were scoping issues remember what the last hit was on the table name and it really just seemed silly about 15 minutes into playing with it so I just got it here and left it. =P

```sh
set tablename=
for /f "tokens=1,2,3,4,5 delims=:'-. " %%i in ('sqlcmd -E -S a_server -d a_sharepoint_admin_db -Q "DBCC SHOWCONTIG"') do @call :check %%i %%j %%k %%l %%m
set tablename=
goto :end

:check
@if %1 EQU Table set tablename=%2
@if %1 EQU Logical if 0 NEQ %4 @echo %tablename% %4
::@echo "%aa%" %1 %2 %3 %4 %5

:end
```

Here is some sample output from dbcc showcontig if you don't know what it looks like:

```plaintext
DBCC SHOWCONTIG scanning 'MSSQLogTemp' table...

Table: 'MSSQLogTemp' (2062630391); index ID: 0, database ID: 32

TABLE level scan performed.

- Pages Scanned................................: 0

- Extents Scanned..............................: 0

- Extent Switches..............................: 0

- Avg. Pages per Extent........................: 0.0

- Scan Density [Best Count:Actual Count].......: 100.00% [0:0]

- Extent Scan Fragmentation ...................: 0.00%

- Avg. Bytes Free per Page.....................: 0.0

- Avg. Page Density (full).....................: 0.00%

DBCC SHOWCONTIG scanning 'Versions' table...

Table: 'Versions' (2073058421); index ID: 1, database ID: 32

TABLE level scan performed.

- Pages Scanned................................: 1

- Extents Scanned..............................: 1

- Extent Switches..............................: 0

- Avg. Pages per Extent........................: 1.0

- Scan Density [Best Count:Actual Count].......: 100.00% [1:1]

- Logical Scan Fragmentation ..................: 0.00%

- Extent Scan Fragmentation ...................: 0.00%

- Avg. Bytes Free per Page.....................: 7562.0

- Avg. Page Density (full).....................: 6.57%
```

This actually becomes much sillier if you consider how easy it is to parse text with LINQ or if you just simply resort to using a DM to get this data. The following works fine and will break it out by each index. =P

```sql
select
      object_name(a.object_id) [table]
    , b.name [index]
    , avg_fragmentation_in_percent [fragmentation]
from
    sys.dm_db_index_physical_stats (db_id(), null,    null, null, null) a
    join sys.indexes b on
        a.index_id = b.index_id
        and a.object_id = b.object_id
where
    avg_fragmentation_in_percent > 0
```

Links for those of you who hate yourselves =P
http://msdn.microsoft.com/en-us/library/ms188917.aspx
http://msdn.microsoft.com/en-us/library/ms188917.aspx
http://technet.microsoft.com/en-us/library/cc966523.aspx
http://www.mssqltips.com/tip.asp?tip=1014
http://msdn.microsoft.com/en-us/library/cc917679.aspx
http://www.sqlhacks.com/Optimize/Fragmented-Data-2005