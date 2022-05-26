---
layout: post
title: print commands to give a group dbo in all the sql dbs
---

something silly i need to do every once in a while and i forget how to do it. the double double single quotes always mess me up =P

```sql
sp_msforeachdb '
    print ''
        EXEC ?.dbo.sp_addrolemember N''''db_owner'''', N''''<GROUPNAME>''''
    ''
'
