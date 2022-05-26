---
layout: post
title: parsing eml files with ruby
---

This is essentially the same thing, just with ruby code. Again, note that you can change that URL regular expression to match what you want to find in the email. i also split the file in half after the header and only searched the header for from/to/subject and the body for urls in this one. a slightly different approach. The point of this is really just to show how simple it is to setup the logic and where you can easily configure your regular expressions to tweak your results. As with most ruby code I write, there is probably a way to do this entire thing in one to three lines. i don't know that way, but there probably is a way. =P

{% gist 45b367a24e58bc81012031d22e2d72ac %}
