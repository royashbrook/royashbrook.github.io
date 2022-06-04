---
layout: post
title: Search all of the text fields in sql server database for a particular value
---

Obviously you can adjust this to taste for different fields, types, outputs, etc. Originally I simply used a select statement and just copy/pasted the gen'd code, but since I was asked for something that would 'do' this, I figured I would go ahead and queue up and exec the commands. I also switched to a CTE just to provide a little separation between 'get the list of tables/columns' and 'produce the list of commands' in case that was needed. The set trans up at the top should remove the need for the with nolocks, but I figured just in case someone snipped that code, they would think twice before issuing broader locking selects in a scope like this so I dropped some nolock hints in. Of course, if you *must* have clean reads, you would have to remove that. This should, according to docs issue and honor no locks during its life but as always, beware when running something like this in prod.

Enjoy!

{% gist de10568859c9b14f34c03b176b5e90f7 %}
