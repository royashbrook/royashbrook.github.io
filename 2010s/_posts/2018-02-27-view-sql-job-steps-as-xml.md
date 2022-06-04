---
layout: post
published: true
title: View SQL Job Steps as XML
date: '2018-02-27'
---
Sometimes I need to look at job steps in SQL. For simple jobs, this isn't a big deal. However sometimes, for a variety of reasons, I just want to get it all in one page so that I can scroll through it. Frequently I simply script the job via create to the page, but this escapes a lot of the characters that I want to see.

Recently the `for xml auto` command popped into my view during a search for something else and I thought I would try that out here. It worked like a champ. If you are in SSMS (at least in version 17.5, which I am using right now) this gives you a little xml blob to click on for a job and that comes up in another window.

I don't think I paid this much attention before because in the results window, you can see the unicode values for some of the things like line feeds, but if you click on it you can see the step data and the commands the same as you would see them in the window.

I don't know that I will use this trick all the time, but it helped me for some side by side comparisons of a few jobs that had very long sql commands in them.

{% gist 5cde18572d35700a1e3181341c7055e0 %}
