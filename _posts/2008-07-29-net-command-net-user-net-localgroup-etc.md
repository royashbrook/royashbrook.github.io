---
layout: post
title: net command (net user, net localgroup, etc)
---

it's depressing to me how many people that i run into today that have an excrutiatingly limited knowledge about the command line in windows, and yet they are considered experts on the platform. that aside, i figured i would share a couple of very simple uses.

something people ask a lot is what domain groups they are in. you can run`net user %username% /domain` from the command line to see what domain groups you are in. it does cut off the names if they are too long, so the ad tools are better or using vbscript to query, but this is quick and easy for general use. there are other uses for this command, but this is the one i use the most often. you can replace %username% with anyone in the domain's ids to view this info. there are some limitations, but it's useful.

another thing that comes up in minor administration tasks is the ability to add a bunch of people at once. you can of course use this with the gui, but if you are already at the command line, you could do something like this on the machine:

``` shell
net localgroup somegroup /add
net localgroup somegroup user1 user2 user3 user4 user5 /add
```

the above commands will add somegroup as a localgroup and then add users 1 through 5 to that localgroup. you can add local users or domain users (via domain\user) or domain groups. 

if it was a sql box, you could then osql locally and run some commands to add them as some role.

``` sql
CREATE LOGIN [%whatever your machien name is%\%whatever your local group name is%]
    FROM WINDOWS WITH DEFAULT_DATABASE=[tempdb]
EXEC master..sp_addsrvrolemember
    @loginame = N'%whatever your machien name is%\%whatever your local group name is%'
    , @rolename = N'sysadmin' (or whatever role you want to give them)
```

there you go. how to add a local group, a bunch of users, and even set them up in sql as some role in like 2 minutes from the command line without waiting for stupid computer manager or sql mgmt studio to open. =) 