---
layout: post
title: Careful with your LINQ joins (WHERE IN TSQL with LINQ)
---

So today I needed to cross reference some stuff in a text file with some stuff in a db. No big deal. Had about 200 unique values to lookup a few pieces of info out of a larger table in a db. The target table had about a half million rows in it, but was indexed on this particular column I needed to lookup on so it seemed like it should be no big deal. I have found myself using LINQPad more and more for little ad-hoc stuff like this.

Anyway, I was prepared to do something similar to this:

```csharp
var list =
    from lines in File.ReadAllLines(@"c:\myfile.txt").Distinct()
    join c in MyTable on lines equals c.MyTable_ID
    select c;
```

The problem with this is it grabs the entire table back and joins it. That's not really what I want and not optimal. If you look at the sql generated it should have one select that grabs the entire table.

However you can do something like this:

```csharp
var list =
    from lines in File.ReadAllLines(@"c:\myfile.txt").Distinct()
    select (
        from c in MyTable.Where (cd => cd.MyTable_ID == lines)
        select new {
            c.field1,c.field2,c.field3
            }
    ).SingleOrDefault();
```

Now this query will generate a bunch of separate queries, each will be a query for the fields you request with just the single id you asked for. This will also give you a null record for non lookups, so you could figure out a way to handle it. Or at least see the empty lookups. This might be what you want, but in my case, I wanted a single query. I was hoping to generate an 'IS IN' type tsql where statement. One single query. The query above *did* reduce my query time from 'OMFG this is never going to complete and people will hate me for hanging up the server' to about a minute or two.

In contrast, I settled on this statement:

```csharp
var list = from c in MyTable.Where(cd =>
    File.ReadAllLines(@"c:\myfile.txt").Distinct().Contains(cd.MyTable_ID))
    select new {c.field1,c.field2,c.field3};
```

This statement took about 10 seconds and produced the query I was looking for. A big parameterized dealio that passed in a bunch of stuff for a 'where xx in yyy' clause.

What was also interesting was where the text manipulation took place in the queries vs sql or in IL depending on how I structured the LINQ statement. When I concatenated field2 and 3 inside of the select new statement in the second statement, it created a subselect in each statement that concatenated the two and then filtered by the id, when I did it in the third statement, it didn't do anything with it in the sql and just did it on the client side.

I'm a huge fan of LINQ now after being a hater for quite a while for it being yet another tool to distance developers from SQL. I use it a lot for adhoc reporting stuff and a variety of analysis. I'm not sure if you would want to do something like this on a regular basis in a regular app, but not sure why not. I'd be skeptical about unattended logic crossing machine boundaries like this on a regular basis in a middle tier somewhere, but I sure do love the versatility for my purposes. You can easily do all this in vs, but I gotta give some mad mad props to the LINQPad guys for a really great product. The main point of this article should be that just because you are using LINQ doesn't mean you can't take some time and really analyze what you are doing to make sure you are doing things the right way. I have seen several articles and blog entries slamming various orms or data access methodologies. Generally, they all seem to do what they are supposed to do for the most part if you just use them correctly and pay attention to what you are doing.

[edit: scott hanselman blogged about this as well (although better) so here's a [link](http://www.hanselman.com/blog/TheWeeklySourceCode52YouKeepUsingThatLINQIDunnaThinkItMeansWhatYouThinkItMeans.aspx?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+ScottHanselman+%28Scott+Hanselman+-+ComputerZen.com%29) to that too =P]