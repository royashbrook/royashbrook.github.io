---
layout: post
title: find sql users and roles assigned to my databases
---

thanks to [this](http://blogs.conchango.com/jamiethomson/archive/2007/02/09/SQL-Server-2005_3A00_-View-all-permissions--_2800_2_2900_.aspx) article for the base script. i needed to get a list of all users and the roles they had in each db. this script is great in that you can use the raw base script (mine is slightly modified) and list things for every object instead of just at the db level. in my case and in most people's case i imagine that makes this dataset rather large. this allowed me to get a large union on the raw data i needed for all of the databases and then i could put it one table so i could work with it to restructure a lot of permissions without having to requery over and over again. i don't have sa rights so this also gave me the ability to ask for sa rights briefly in order to accomplish this and then i could be taken off. if you had a less nice sa, this should give you the ability to give them something that only takes them like 10 minutes to do. =)

 mostly like all things i'm just posting this so i don't forget it ha!

I just did this:

```sql
sp_msforeachdb 'print  ''
USE ?
GO

WITH perms_cte as(
    select
        USER_NAME(p.grantee_principal_id) AS principal_name
        , dp.principal_id
        , dp.type_desc AS 
        , p.class_desc
        , OBJECT_NAME(p.major_id) AS object_name
        , p.permission_name
        , p.state_desc AS permission_state_desc
    from
        ?.sys.database_permissions p
        inner JOIN ?.sys.database_principals dp on
             p.grantee_principal_id = dp.principal_id
    where
        class_desc is null
)
SELECT
    *
INTO
    somewhere.DBO.R1_?
FROM (
     /* users */
    SELECT
        [db]=db_name()
        , p.principal_name
        , p.class_desc
        , p.[object_name]
        , p.permission_name
        , p.permission_state_desc
        , cast(NULL as sysname) as role_name
    FROM
        perms_cte p
    WHERE
        principal_type_desc <> ''''DATABASE_ROLE''''
    UNION
    /* role members */
    SELECT
          [db]=db_name()
        , rm.member_principal_name
        , rm.principal_type_desc
        , p.class_desc
        , p.object_name
        , p.permission_name
        , p.permission_state_desc
        ,rm.role_name
    FROM
        perms_cte pright
        outer JOIN (
            select
                role_principal_id
                , dp.type_desc as principal_type_desc
                , member_principal_id
                , user_name(member_principal_id) as member_principal_name
                , user_name(role_principal_id) as role_name
                /* ,* */
            from    
                ?.sys.database_role_members rm
                INNER JOIN ?.sys.database_principals dp ON
                    rm.member_principal_id = dp.principal_id) rm ON
            rm.role_principal_id = p.principal_id) X''
'
 ```
  
 Then ran the results to get the data for each db, then I ran this:
 
 ```sql
 sp_msforeachdb 'print ''UNION ALL SELECT * FROM somewhere.DBO.R1_? '' '
 ```

and removed the first union and I had my data. Plus I have all the root tables in the somewhere database to work with temporarily.