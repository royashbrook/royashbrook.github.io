---
layout: post
published: true
title: Invoke-ExcelQuery on ImportExcel
subtitle: >-
  Using SQL queries to pull data out of Excel using the ImportExcel PowerShell
  module
date: '2021-11-13'
---
[ImportExcel](https://github.com/dfinke/ImportExcel) is a wonderful module that lets you interact with Excel files using powershell without having Excel installed. I have been using [@DougFinke](https://twitter.com/dfinke)'s module to great success fo some time, however for some situations I found it easier to manage my interaction with Excel files using SQL.

To accomplish this I was using oledb to query the file. I was just copy/pasting a function around as needed. Since I used ImportExcel already, I thought it would be nice to integrate this with that module. I thought perhaps my use was an edge case, but what better place to add it if someone else could use this?

I submitted a [feature request](https://github.com/dfinke/ImportExcel/issues/1099), and then a [PR](https://github.com/dfinke/ImportExcel/pull/1101) to [@DougFinke](https://twitter.com/dfinke)'s [repo](https://github.com/dfinke/ImportExcel), but also went ahead and published a [Read-OleDbData](https://github.com/royashbrook/Read-OleDbData) module to give [@DougFinke](https://twitter.com/dfinke) something to look at.

[@DougFinke](https://twitter.com/dfinke) worked with me to get it up to release/addition standard for his module, and as of v7.3.1 this ability is now available to everyone that uses [ImportExcel](https://github.com/dfinke/ImportExcel)!

[@DougFinke](https://twitter.com/dfinke) wrote up some nice [examples](https://github.com/dfinke/ImportExcel/tree/master/Examples/InvokeExcelQuery)
 and a great intro [video](https://www.youtube.com/watch?v=_7xuhsZm0Ao). Copy/paste from the examples below.
 
```ps1
$queries = 
'select * from [sheet1$A:A]',
'select * from [sheet1$]',
'select * from [sheet1$A2:E11]',
'select F2,F5 from [sheet1$A2:E11]',
'select * from [sheet1$A2:E11] where F2 = "Grocery"',
'select F2 as [Category], F5 as [Discount], F5*2 as [DiscountPlus] from [sheet1$A2:E11]'

foreach ($query in $queries) {
  "query: $($query)"
  Invoke-ExcelQuery .\testOleDb.xlsx $query | Format-Table  
}
```

My personal use-case for this was processes that mashed up data from SQL and Excel. In those processes I found it easier to use SQL files to hold the queries. Using SQL files allowed me to use a simple loop where I pulled some data using a 'type' of datasource and a sql file to hold the query.

Any questions and you can hit me up on twitter [@RoyAshbrook](https://twitter.com/RoyAshbrook)

I hope this helps out someone else out there =)