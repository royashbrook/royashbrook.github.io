---
layout: post
title: Search all of the text fields in a sql server database for a particular value... with LINQ (Pad)
---

So, the question was posed on how to do this with LINQPad, and thus with LINQ. Here is one way. This is basically the same thing I did in my last post about searching for this kind of value. This also shows how to return an arbitrary result from a linq query to a very generic class object for later processing. Note that I was not able to use params because the table name couldn't be a param. I'm sure there is probably some more elegant way to do this, but this works fine and hopefully this is one of those scripts/snippets of code that you never have to use. Since you can just do something like this with SQL, I'm not sure of the usefulness of this code in a realistic setting, but I suppose if you needed to throw up a utility page and you already had a LINQ data context in your app for other stuff, you could do something in a pinch using executequery. I'm personally not a big fan of using executequery as it stands today. I generally just take it as a sign I need to switch to sql, but for something like this (I mean, I could be joining on a file with a list of text to look for right?), the versatility is nice to have. =)

{% gist 7f86bfebe1b1ba5a2ea014a7802470a9 %}