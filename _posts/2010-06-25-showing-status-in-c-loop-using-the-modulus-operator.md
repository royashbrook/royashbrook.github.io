---
layout: post
title: Showing status in c# loop using the modulus operator (%)
---

I needed to take some records in one file, do some stuff with the record, then put the results in another file. The 'do stuff' part was taking a bit longer than I thought it would so I wanted to see status. This sample illustrates taking 1000 records from a source file, uppercasing each line, putting it in another file, and letting us know every 100 lines.

{% gist 85909c914cc61cab94451d4dafa51857 %}
