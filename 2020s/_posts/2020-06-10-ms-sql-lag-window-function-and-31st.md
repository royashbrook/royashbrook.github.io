---
layout: post
published: true
title: MS-SQL LAG window function and 31st
subtitle: All 30 day months!
date: '2020-06-10'
image: >-
  https://png.pngtree.com/png-clipart/20190630/original/pngtree-march-31st-date-on-a-single-day-calendar-png-image_4153496.jpg
---
> I will use some dummy tables for this topic. I'll post something to generate some test data and the full SQL down at the bottom. For now it's important to know this will involve a table I'll name @SalesByDay with two fields named SalesDate and TotalSales. This table has Sales totals by day and has a date in the SalesDate field and the total sales for that day in TotalSales.

## Can we see last month's numbers?

I have a dashboard chart that shows sales by day. I have other similar charts that show X-other-thing by day. Requests for 'forecast' data come up for some of these charts. This requires some discussion but eventually we have some forecast data to look at. This gives us 'today' and some way to 'look ahead' which led to the natural next request of a 'look back' view. This presented as 'let's look at this value on the same day last month.'

Well, there aren't the same number of days in each month, but that problem was easy to solve with some discussion and it was determined that looking on the same date of the month is sufficient. I use a Date table for various date info, so initially this was accomplished with a simple look back based on the previous month and the day of the month via select top 1 subselect. This would yield nulls for the days where there was no day info, and provide the proper info based on the last month and day of the month as needed. While I was doing some other queries in another dataset that did not have a date table for this, I decided to try out the lag function. I was 'aware' of these functions, but hadn't found a use that really demanded them much before. In this case, I could work around it, but upon testing it out the window functions seemed **much** more performant than what I was doing. Great!

## That's weird...

So I switch over the the lag function. I partition by the day of the month value, then order by date and go back 1 value. So all the 1st are together, and the 2nds and so on as part of the partition, then you would go back one row to get the value from the previous month. If you aren't familiar at all with this function, I'd suggest checking [this article](https://www.sqlservertutorial.net/sql-server-window-functions/sql-server-lag-function/) as I feel it does a great job explaining it with some sets of data and arrows.

This works great and everyone is happy. While this is being setup, COVID-19 happens. Why does that matter? Well mainly because I work for a transportation company that moves fuel around amongst other things. As you can imagine, with people staying home, there is a dramatic difference in how much fuel we move around pre vs post stay-at-home guidance.

In May I notice that the 'previous' numbers for the end of the month seem to spike up. Weird, but maybe more info got put in or more business got processed at the end of the month. I'm not an expert in our operations, so sometimes oddities like this are explained away with some special operational processes that I'm not familiar with. I ask a couple of people, no one seems concerned. OK, then. 

Spoiler Alert: this is because things are not actually 'OK'.

## What's wrong?

If you found this page by searching for anything about this problem, you are already familiar with this issue. The problem is that if you are missing a previous day in a month, LAG will simply go back to the actual previous value in that partition. So if you are in May, and you are on the 31st, and you look back to the 'last 31st day value) you will get the value from March. This produced a large spike as we had understandably reduced volume in april, similar to may but not as bad, but no where near as high as in March before all of the changes had really taken effect. So a big spike on the 31st because we have March's data.

Here is a table that shows the SalesDate, the TotalSales. It also has SalesPrev which is using the lag function without any other help, and a 'Fixed' column which shows the correct value. You can see the items in SalesPrev in bold are pulling from the incorrect month. 

|SalesDate |TotalSales|SalesPrev|Fixed   |
|----------|----------|---------|--------|
|2020-01-29|341.94    |NULL     |NULL    |
|2020-01-30|**11924.09**  |NULL     |NULL    |
|2020-01-31|**46555.22**  |NULL     |NULL    |
|2020-02-29|6072.08   |341.94   |341.94  |
|2020-03-29|73459.94  |6072.08  |6072.08 |
|2020-03-30|50923.97  |**11924.09** |NULL    |
|2020-03-31|**16432.06**  |**46555.22** |NULL    |
|2020-04-29|57900.76  |73459.94 |73459.94|
|2020-04-30|60334.48  |50923.97 |50923.97|
|2020-05-29|9355.15   |57900.76 |57900.76|
|2020-05-30|75511.38  |60334.48 |60334.48|
|2020-05-31|24902.04  |**16432.06** |NULL    |
|2020-06-29|73352.32  |9355.15  |9355.15 |
|2020-06-30|38204.15  |75511.38 |75511.38|

This only happens on days where you don't have the same day the previous month, but you *do* have some data in your lag set from a month that has this value. Originally this did not show up for me as I was pulling only the previous month, so I would get null when appropriate. But eventually I added some additional data so we could run some rollup data for previous months as well in other charts and then it popped up.

## OK. So... how to fix?

The fix for me was pretty simple. I just wrapped the lag value in a case statement and made sure the day in question was less than or equal to the last day of the previous month. So if it's the 31st, but the end of the previous month was the 30th, return null. Here's the snip that produced the value above. Note the formatting is just there to pretty up the output from the [csv-markdown-converter](https://www.convertcsv.com/csv-to-markdown.htm) I used above.

``` sql
select
	  [SalesDate] = cast(format([SalesDAte],'yyyy-MM-dd') as varchar(10))
	, [TotalSales]
	, [SalesPrev] =
		lag([TotalSales]) over (partition by day([SalesDate]) order by [SalesDate])
	, [Fixed] =
		case
			when day([SalesDate]) <= day(eomonth(dateadd(month,-1,[SalesDate])))
			then lag([TotalSales]) over (partition by day([SalesDate]) order by [SalesDate])
		end
from
	@SalesByDay
where
	day([SalesDate]) in (29,30,31)
order by
	SalesDate
```

## The End

I feel like there is probably a more elegant way to do this. I played with some other ways like skipping a partition and calculating the rows back or doing something based on info in my date table. But this was simpler for me to understand generally and worked perfectly, so I left it at that.

Some items I searched for that didn't help me at all, but maybe it'll help someone find this page:
- lag rownumber missing rows sql
- sql window function get value from same day last year
- window function get value from same day of last month sql
- lag doesn't work for "31" sql
- lag doesn't work for "31sts" sql
- lag doesn't work for 31sts sql
- sql lag partition by how to deal with nulls

Here is a gist with the example code used here:

{% gist ac059ff0359a23bc5c5227bb7881d90b %}
