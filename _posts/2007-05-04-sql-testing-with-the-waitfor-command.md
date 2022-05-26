---
layout: post
title: SQL Testing with the WAITFOR Command
---

The WAITFOR command isn’t exactly new. But, I don’t really use it much. I did however recently have a desire to do some duration testing that wasn’t something akin to ‘insert 100000 rows into a table and see if stuff slows down.’ I wanted to simulate say a couple of transactions a second for a short period of time. Just a few minutes, really. I probably would have turned to writing some sort of mini test application for c# or writing a unit test that had a loop in vs. with a delay in it. Something like that. Fortunately, for me, I had recently perused and article about SQL2005 that had come up on my search for bulk importing…. another subject I intend to blog about soon. =P

This article was pretty old and spoke to the new changes in Yukon aka SQL2005 and one of the new enhancements was the WAITFOR command.

I’m just going to cut and paste from the [MSDN article](http://msdn.microsoft.com/msdnmag/issues/04/02/TSQLinYukon/default.aspx) here. If you ever have a simple need to test how a system works and need to simulate some simple stress like this, check it out and I’m sure you can imagine some uses. I’m a big fan of adhoc testing as something that you can do off the cuff to rule out or highlight problems. Enjoy!

> The WAITFOR Command
> 
> The WAITFOR command has been enhanced in several ways in Yukon. Besides waiting for a specified duration or until a certain datetime value, you can now request to wait for a T-SQL statement to affect at least one row. You can specify that the command wait on one of the following statements: SELECT, INSERT, UPDATE, DELETE, or RECEIVE. The first four are self-explanatory; RECEIVE refers to receiving a message from a queue. You can optionally specify a timeout if you want to stop waiting after your specified number of milliseconds. The syntax of the WAITFOR command is:
> 
> Another T-SQL enhancement in Yukon allows you to return output from Data Manipulation Language (DML) statements other than SELECT (INSERT, UPDATE, DELETE). A new OUTPUT clause allows you to request that the old/new images of the columns be returned by referring to the INSERTED and DELETED tables, similar to the way you refer to them in triggers. You can even specify an INTO clause and direct the output to a table variable. Another enhancement lets you specify the READPAST hint with modifying statements, allowing you to skip locked rows.
> 
An example of using the aforementioned enhancements is to have several processes waiting for a DELETE statement to delete at least one row from a table, directing the output to a table variable, with each processing different portions of the data in parallel. To witness this, create the following MsgQueue table:
> 
> 
``` sql
USE tempdb

CREATE TABLE MsgQueue
(
msgid INT NOT NULL IDENTITY PRIMARY KEY,
msgdata VARCHAR(15) NOT NULL
)

--Open one or more connections and run the following code in each to periodically insert new messages into the table:

SET NOCOUNT ON
USE tempdb

WHILE 1 = 1
    BEGIN
        INSERT INTO MsgQueue
        select 'Msg' + CAST(
            CAST( RAND()*1000000000
                AS INT )
            AS VARCHAR(10) )
        WAITFOR DELAY '00:00:01'
    END
```

Next you need to open several other new connections and run the code in [Figure 4](http://msdn.microsoft.com/msdnmag/issues/04/02/TSQLinYukon/default.aspx?loc=&fig=true#fig4) in each connection to simulate processing of the newly arriving messages.

