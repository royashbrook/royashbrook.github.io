---
layout: post
title: tsql replace vs ltrim/rtrim performance
---

so after looking at a bunch of ltrim/rtrim statements that were just getting rid of the spaces, i wondered if i had values padded with spaces, would it be faster just to use the replace function in sql. the short answer is no. here's some test code. you should be able to mix and match or create comletely isolated sql statements if you like. the results were all the same when i tested, replace was always slower. increase the count if you want for a larget diff. 

{% gist 76622d72401c537298b49ebecd1e0848 %}