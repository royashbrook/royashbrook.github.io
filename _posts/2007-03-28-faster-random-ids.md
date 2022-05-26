---
layout: post
title: faster random ids
---

i previously blogged about getting random ids from a sql table for testing. i was thinking about it and calling the entire table for a scan and using newid to sort seemed like it would be slower than selecting a random number from the possible range of ids using rand. now, note that this only works if you have an integer based value and if you know the values will be there.  i suppose you could put a safety loop that wouldn't let it out until it found some random value, but ... well whatever. personally, i don't mind missing numbers showing up here because generally i'm only using random numbers for testing anyway. this method is really a different animal from the orderby newid() since this query has the intention of selecting a single record randomly, whereas orderby newid() queries often are returning all the records just sorted in a random order. as always, ymmv and test things yourself first. here's some code:

{% gist 0272a37ebba23a5f12bbec7b804854c4 %}