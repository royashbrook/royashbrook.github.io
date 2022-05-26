---
layout: post
title: nopatchforstupid #4
---

## stupid

``` SQL
select f1 from t where ltrim(rtrim(f2)) != ''
```

## not stupid

``` SQL
select f1 from t where ltrim(f2) != ''
```

## why?

you could use ltrim or rtrim by itself. why are you trimming both sides when you are searching for an empty. if it trims to empty, then either one will result in the same. the only time you would need both is if you actually want to trim a string on both sides, here you just want to exclude strings with just spaces.
