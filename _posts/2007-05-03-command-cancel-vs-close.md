---
layout: post
title: command cancel vs close
---

There are a ton of articles about using command cancel vs close when you are using a datareader. The idea is that it will make things faster if you cancel the command rather than just closing it if you have an occasion to interrupt the execution. If you don’t cancel, it seems that it will read the rest of the items even if you break the loop, it just won’t do anything with them. Since you are breaking out, you normally don’t need to process the rest of the records, and so you can simply cancel the execution of that command object.

Here’s some posts and/or articles about it:

http://authors.aspalliance.com/aspxtreme/sys/data/sqlclient/SqlCommandClassCancel.aspx
http://www.developerdotstar.com/community/node/531
http://msdn2.microsoft.com/en-us/library/system.data.sqlclient.sqlcommand.cancel.aspx

Here’s a sample code snip that shows what I’m talking about.

{% gist 595c76f3f31092be33d2cc2b70b8cf7f %}