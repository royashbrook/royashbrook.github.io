---
layout: post
title: ah yes... LINQ is case sensitive...
---

So I am a huge fan of LINQPad. I use it all the time for ad hoc data processing mostly because of the .Dump() method which allows me to copy and paste something a little prettier than what I get outta mgmt studio or a simple command line processor. It's really pretty much the same to me, but for some reason normal people seem *wayyyyy* happier when they get a little blue box with some lines instead of just text with tabs or commas =P

Anyway, I was fixing to import some data from a file and I was joining it against the db, but there were some values to lookup that in the import were text files. No big deal, just join the values and get the ids based on the names. This did work, but I was trying to shrink the data being sent/retrieved over the wire so I did a pre-fetch on the distinct values and joined them on the ids on the lookup table. This worked fine, but I found that when I later went to join the object I retrieved against my file imported object, things didn't match. This made me a little crazy for about an hour or two as I kept coming back to it and writing queries that would show more and more of the data. It was particularly annoying because usually I just import this data into a table and do it all in sql, but I figured at some point I wanted this to be some kind of GUI so I'd do it in linqpad in preparation of using EF or Linq2Sql or whatever more .net'ey way of doing things. So since I have done this about 50 times in the past with no problems, it was irritating that it was causing me any time at all. Trying to 'research' what was up with my data caused me to need to do an outer join which is its own particular flavor of silliness (IMO) in LINQ.

So I let it sit overnight. Came back the next day and realized what was up. I was doing a linq2objects compare really because I was crossing the retrieved data (an anon type with just my id and name I joined with the distinct values from my import file) with my import data (a anon type with the stripped lines from my import csv) and linq2objects is case sensitive. For me, I am simply dealing with basic imports with all English chars etc so .ToUpper() on both sides of the compare fixed everything. But if you have to deal with larger international type imports with different globalization/culture concerns, you might need something else.

Anyway, here's a little code snip even though this almost doesn't really warrant one. There are some other ways to 'join' this data, but this is basically going to get the TableInDB and then It will do the compare client side, that's why it fails without the case insensitivity.

```csharp
var data_with_keys_to_import =
    from f in DataImportedFromFile
        join t in TableInDB on d.TextValueForKey.ToUpper() equals t.TextValueForKey.ToUpper()
    select new {...};
```

And here's a few of links about case sensitivity stuff if you can't find your own on google/bing. I found the last one the most poignant.

http://stackoverflow.com/questions/841226/case-insensitive-string-compare-in-linq-to-sql
http://blogs.msdn.com/b/michkap/archive/2007/10/01/5218976.aspx
http://blogs.msdn.com/b/michkap/archive/2005/03/10/391564.aspx