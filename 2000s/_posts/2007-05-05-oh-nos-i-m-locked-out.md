---
layout: post
title: OH NOS!!@@ I'm Locked OUT!
---
So I ran into a situation today where I needed to rename a database. Unfortunately, unknown to me, I had that database setup as my default database. Although I had SA role in sql, as soon as I took the database offline to rename it, I couldn’t do anything. Unfortunately, I didn’t notice the problem right away because, I wasn’t really doing anything else on the box at the time except running some other queries and those were already logged in to other databases. After about 15 minutes I went to get a drink and when I came back it was STILL not done. So, I started to poke around and noticed I can’t get to anything. I was getting access denied to db1 (my default) when I clicked on db2 and db3. “Crud.”, Says I. What do you do?

Well first I examined my method of access. I was a member of several groups that had this default db set. This default db setting was not something that *should* be set, but it was so oh well. I think executed a create login statement for myself giving myself an account with a default db of master to override the other settings. Here’s a sample:

``` sql
USE [master]
GO
CREATE LOGIN [DOMAIN\ROY] FROM WINDOWS WITH DEFAULT_DATABASE =[master] , DEFAULT_LANGUAGE =[us_english]
GO
```

If you already have a login, just issue an alter it. Checkout BOL for some guidance on this. So I take a look at the sql security and see that some of the groups I’m getting rights from (all windows auth). But what if you didn’t already have a window open? Ah, have no fear my friend. You can always use osql or some program with a custom connection string etc. SQL Management Studio hides the option a little bit from you. When you connect to something via connect button or modifying the connection settings of an existing window, you get the nice little login dialog box. If you click on options, you can change the database at the top.

http://drowningintechnicaldebt.com/blogs/royashbrook/oh_nos_im_locked_out/image001.jpg

Oh Nooooooo!!! I can’t see any of the databases because I don’t have rights to connect to the server! That’s fine, just put in master or some other db you have rights to. Simple. Now I’m in. The problem is that my ‘take offline’ operation is still pending and if you try and look at the db you wanted to take offline, you see that it’s ‘in a state of transition’ and it will not let you look at it. By this time, the server dbas were calling me wanting to know what was up (they got a call from another dba who had the incorrect default db set too) and I filled them in on what was going on. I told them my next plan of action was to kill my spid and then to continue with the rename operation if the db was offline after the spid was killed, or to handle it off hours since the db was taking so long to go offline. They complained at me that they didn’t want app dbas that happened to have sa rights for app optimization purposes (dbcc commands etc) doing real sa work. So I gladly offloaded the ‘work’ of renaming the dbs to them and before I could even finish talking to my boss about what had happened, they had already killed the spid and everything just went back to normal. Pretty cool.

So, I say this because I have had issues in the past where I or another person had accidentally removed a db that was their default (or mine in this case 😛) and I figured I’d let someone else know what to do.