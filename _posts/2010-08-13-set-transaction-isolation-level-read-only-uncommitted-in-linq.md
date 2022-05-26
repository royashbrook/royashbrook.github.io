---
layout: post
title: set transaction isolation level read only uncommitted in LINQ
---

apparently you can use a transaction scope for this and just wrap everything that way. but to me that seems more complicated than just enabling it with execute command.

some LINQ code like (note that ‘this’ is a data context):

```csharp
this.ExecuteCommand("set transaction isolation level read uncommitted"); 
MyTable.Take(5).Dump();
```

Produces this sql code:

```sql
set transaction isolation level read uncommitted 
GO

SELECT TOP 5 <fieldnames…> FROM [MyTable] AS [t0] 
GO
```

That’s what I want to see, so yay. =)

some links:
http://madprops.org/blog/linq-to-sql-and-nolock-hints/
http://www.hanselman.com/blog/GettingLINQToSQLAndLINQToEntitiesToUseNOLOCK.aspx

ps. trying to use livewriter today. so far, not excited about it and still like word more =P hopefully i’ll get elightened.


