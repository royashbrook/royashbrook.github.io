---
layout: post
title: delete objects belonging to a schema in sql
---

so you try to wack a crazy user who created stuff under his/her own schema in a sql database, but when you try to drop the schema, you get the evil 'something is owned' message. then you look and see they have a billion objects. booo..... oh well, not too big of a deal. you can use something similar to below. the below just drops syn's but you can put a case statement in the build portion based on type and drop everything you need to. obviously this can be tweaked some based on your taste. 

{% gist 2c1ee35894ca7980761f76f21c06672f %}
