---
layout: post
title: SQL Query Processing
---

There are some distinct steps that take place when you submit some tsql to a mssql server. Here's a short overview of them.

1. Parse Query - Check Syntax, Store Query
2. Standardization - Prepare query for optimization
3. Optimize
4. Execution

Let's look at #3 briefly. MSSql does Cost Based optimization which means it will utilize data it knows about the tables involved (statistics) to determine a cost for each possible execution path. It will then pick the lowest cost. MSSql has three steps to it's query optimization.

1. Query Analysis - Identify optimizable clauses (x=1, name='fred')
2. Index Selection - Choose indexes that have a lower cost than a table scan
3. Join Selection - Identify which join order will have the lowest IO cost

There is a lot more information available about this out on the web and in books, but this gives you a rough idea of what happens when you send data to sql.