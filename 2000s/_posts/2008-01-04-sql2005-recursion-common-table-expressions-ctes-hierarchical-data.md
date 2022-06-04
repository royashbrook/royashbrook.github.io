---
layout: post
title: sql2005 recursion, common table expressions (CTEs), hierarchical data
---

A colleague of mine that is more familiar with Oracle than MSSql asked me how to create a CONNECT BY query in TSQL. CONNECT BY queries provide for recursive results or views on hierarchical data. The answer is to use a Common Table Expression or CTE. Here is a very simple example that is actually pretty close to what he needed. This assumes a very basic table named 'categories' with an int id, name varchar, int parent_id column and you can put in your constraints. it holds categories and parent categories. the top level will have a null for a parent_id.

``` sql
with cte(name, id, lvl, sk)
as
(
    select
        cast(name as varchar(255))
        , id
        , 1
        , cast(enum_cat_desc as varchar(255))
    from
        dbo.categories
    where
        parent_id is null
    union all
    select
        cast(space(hl * 5) + n.name as varchar(255))
        , n.id
        , cte.hl + 1
        , cast(sk + space(1) + n.name as varchar(255))
    from
        dbo.categories n
        join cte on
            cte.id  = n.parent_id

)
select
    name
from
    cte
order by
```