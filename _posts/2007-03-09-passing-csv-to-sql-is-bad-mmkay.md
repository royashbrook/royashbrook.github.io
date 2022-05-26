---
layout: post
title: passing csv to sql is bad mm'kay
---

passing csv to a stored proc is bad m'kay. it leaves you open to sql injection attacks and in general isn't the best practice anyway. i'm going to avoid entire code samples just because it's too much typing, but let's take a sample proc and assume you are passing in a varchar(1000) (@csv) comma delimited string. you could do something like this:

``` SQL
declare sql varchar(2000)
set sql = 'select * from table where id in (' + @csv + ')'
sp_executesql @sql
```

obviously this will work fine if you pass in 1,2,3,4 into your list. but what if some bad person sends you 1,2,3,4,null);select * from users; or something more fun. yep, bad. so what can we do? xml my friend. let's change our input parameter to a type of xml and name it @xml. let's say our xml must be in the format and you can have as many r records as you want. in fact, they can be any kind of record for this following query as long as they have an id attribute.

``` SQL
select * from table t join @xml.nodes('/root/*') x(n) on n.value('@id','int') = t.id
```

yep, same thing. you can do this a little differently using openxml, but that's old news and you can look that up. (via don't feed the penguins)


*edit*
also, don't forget that you can use more basic xml structures like just using value records and no root or wrapper. you can also validate the xml using xml schema collections in sql so you can have strongly typed values passed in.

the *point* is that you shouldn't be using csv values to pass to a proc. it's actually way easier in the sql code to use xml, and it's not tough in your calling code to send in <v id="1" /><v id="2" /><v id="3" /> instead of 1,2,3. yes, much longer which i'm normally against, but your sql isn't much longer and it performs much better too.

__Edit from the future!__

> Here in the year 2018, I have a new gist to add to this entry :-)

{% gist a26f23c6364670205b48d5f98e113456 %}