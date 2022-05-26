---
layout: post
published: true
title: Listing Documents via Jekyll without collections
image: 'https://images-na.ssl-images-amazon.com/images/I/61MtgDxU%2BUL._SY355_.png'
subtitle: >-
  How to avoid configuration files for document meta-data? Just use the file
  names!
---
Recently, I moved all of our static sites over to hosting on github using [github pages](https://pages.github.com/). Github uses [jekyll](https://jekyllrb.com/docs/home/) for generation of the static content and there are tons of themes out there to utilize.

You can also just publish straight up html files and it will work the same way. The ins and outs of all of this is better left to the links above.

This worked really well for us, however we had one issue where we needed to publish lists of pdf documents, but I wanted to have the lists generated automatically. These documents had some meta data, but I didn't want to have to maintain a [collection](https://jekyllrb.com/docs/collections/) to be updated or use some other data structure. The goal was for me to be able to hand off the 'updating' of the site to someone else and make it very simple. So I wanted to just use the file names to hold the metadata. I decided on a format something like `DATE-TITLE-TYPE.pdf` for my first process. And to keep the different documents for different pages away from each other, I put them into different sub folders in assets. The mechanism for getting at these files without any other data structure is to use [site.static_files](https://jekyllrb.com/docs/static-files/) which is already available to us.

So in a simple example site structure we would see:

{% gist a4ddefc439e578792dceecae59bc0234 dir.txt %}

And then on page1.md, to show these files, I did something like this:

{% gist a4ddefc439e578792dceecae59bc0234 page1.txt %}

And this would generate a list of links to the individual pages. You can also filter based on the t0/1 document type if you want to assign with another filter and you can chop up the name however you like or using whatever format you like. I am just using three fields, splitting on `'-'` and leaving off the 'type' code that I put on the end with t0/1 in this example.

In my case, I did get quite detailed and had a few different structures for each of the items. You can definitely make a case *not* to do this, but the goal was to make this easy for other people to add content and in all of our particular use cases, we are just adding items to show up in a list. Maybe a couple of items a quarter or so. So this way I could make the instructions something like 'name the file this, then copy it here' and that's it. =)

You could also put these files in any folder, I'm guessing. So if you had, say, 'licenses' that had to be renewed from time to time, you could just create licenses.md and make a licenses folder and filter based on that in the path. I just decided to put things in assets to keep the root dir clean.

I would say the biggest challenge for my use case was trying to chain together too many pipes in the coding. The fix for this was usually just to be very specific with a number of assign statements and maybe add some looping in if I was appending more complex things to an array collection or something similar.

After the update, however, I did have to publish and update, and this made it a super easy drag/drop/push experience!

![easy](https://static1.squarespace.com/static/564e0683e4b094eaa9d90b4b/t/58ee95a9f7e0abff4db166fb/1492030892939/)
