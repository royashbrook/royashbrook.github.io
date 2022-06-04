---
layout: post
title: Amazon S3 Outage
---

If you haven't heard about it, Amazon had an outtage the other day with some of it's shared services. Here's a great article explaining "what up with that"

http://www.roughtype.com/archives/2008/02/why_s3_failed.php 

Here's a quote from Amazon about what happened:

> Early this morning, at 3:30am PST, we started seeing elevated levels of authenticated requests from multiple users in one of our locations. While we carefully monitor our overall request volumes and these remained within normal ranges, we had not been monitoring the proportion of authenticated requests. Importantly, these cryptographic requests consume more resources per call than other request types.
> 
> Shortly before 4:00am PST, we began to see several other users significantly increase their volume of authenticated calls. The last of these pushed the authentication service over its maximum capacity before we could complete putting new capacity in place. In addition to processing authenticated requests, the authentication service also performs account validation on every request Amazon S3 handles. This caused Amazon S3 to be unable to process any requests in that location, beginning at 4:31am PST. By 6:48am PST, we had moved enough capacity online to resolve the issue.

I've been using many of amazon's projects for off and on development projects pretty much since they came out and have always found their services reliable and pretty easy to use. 

