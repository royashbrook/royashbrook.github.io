---
layout: post
title: how to graph / chart multiple ping results using shell scripting and logparser
---

how to graph / chart multiple ping results using shell scripting and logparser
i probably should have called this post 'stupid batch file tricks' or 'how much time do you want to waste on batch files' as well. =P anyway, i needed to graph something else, and i was fiddling around with logparser. if you have ever fiddled, you have probably googled, and if you have ever googled for logparser, you probably have come across Ken Schaefer's blog post about graphing ping results. it's [here](http://www.adopenstatic.com/cs/blogs/ken/archive/2005/05/30/22.aspx) if you haven't. go check it out. i will wait. it's quite short. wayyyy shorter than this. =P

anyway, i wanted to represent several test result series from servers, but i wanted to be able to basically just write whatever tests i wanted to do whatever and just use shell script so i could do whatever pretty easily. to do this i needed a simple execution harness and a way to output the data as a table with multiple series. you can get this data in one big table and then pivot it, but i wanted to just grab the data once and not massage it and this was pretty simple to do just using some cheating at the command line. the easiest test to do was a ping test so i just wrote a little poc script to do ping tests and dump the data to a tsv. then it outputs a logparser command that will show the chart. obviously, tweak everything to 'taste' lol

{% gist 81f0e407d27dee0f192a58a95db338af %}

before you ask:
- yes you can just dump this to a series in anything that can do pivots, and then just pivot it
- yes you can do this with excel by just copying and pasting
- yes.... you can automate excel and do it that way =P
- yes.... you could use xml or any of a million other ways to do it
- yes, you should be able to nest the commands using parens instead of calls/gotos, i just did that while i was troubleshooting and since it worked, i didn't see any great reason to undo it
- yes you can use goto :EOF, see #5 on why this isn't
- and finally... yes there are a million other ways. but this was pretty simple and it was more of a thought exercise and seemed worthy of a blog post due to the .... probably less than normal solution lol
the logparser command looks something like:

`logparser "select test#,s1,s2,s3,s4 into output.gif from output.tsv" -i:TSV -charttype smoothline -view`

and output.gif from that command looks something like:

![sample ping results]()

anyway, next i will post a list of my fav logparser links. =) there's a ton to be said about logparser, but most of it is already in the links i will post next time.