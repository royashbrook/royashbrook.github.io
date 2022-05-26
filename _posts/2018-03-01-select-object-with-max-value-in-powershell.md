---
layout: post
published: true
title: select object with max value in powershell
date: '2018-03-01'
---
Today I was working on a powershell script that batch sends out some files. The files are generated in blocks of n records depending on what is new. so sometimes there are 1 and sometimes there maybe be 10. The files names are formatted like `{0:yyyyMMddHHmmss}_export_{1}.txt` but they are produced sequentially so the create/write times may not be the same.

The current process generates the files and then removes them, but due to some previous troubleshooting issues, I want to leave them for a week or two and just send the latest files.

To accomplish this, every time the process runs instead of just selecting all of the `*.txt` files I need to figure out what the latest 'batch' of files is and select all of those.

{% gist 95b6d08f17d0910243c6f3c6e7934d70 %}

I could also just build an array of the file names that were created during this run, but currently the script isn't architected to pass that kind of data arround since due to includes and I didn't want to create a global variable to hold it. The downside of this is that if the file format changes it will break how we figure out the filter, but that's a different problem than getting the object with a max value. =)
