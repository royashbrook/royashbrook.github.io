---
layout: post
published: true
title: Uploading data to o365/spo with Powershell
date: '2020-06-24'
subtitle: ...finally figured it out
image: /img/finally.png
---
I have a static website that I host on o365 for some internal use. I upload some data that is rendered via js at some scheduled times. This works today but I wanted to deploy code via script, so I took another look at how I was doing things to refine it.

While there are a lot of ways to do this, I would point to the following two articles as the most helpful to me personally:

- [Older Article](https://support.microsoft.com/en-us/help/2529243/sharepoint-2010-howto-upload-a-file-of-the-size-of-1-gb-using-client-o)
- [Newer Article](https://docs.microsoft.com/en-us/sharepoint/dev/solution-guidance/upload-large-files-sample-app-for-sharepoint)

Many years ago I did more fun things with sharepoint, webparts, and just things like that in general. But for many years my use of sharepoint has been pretty typical with the occasional desire for some slightly more advanced views or perhaps hosting a static site with some data or something similar. I say that because my familiarity with the object model is utilitarian in nature and as I was going through this exercise, I ran into a lot of gotcha moments that were simply due to my lack of understanding and my desire to just rush through to a functional/working solution.

Here is what I ended up using, although I am adding some heavy annotation here to cover the items that caused me some issues.

{% gist ef24c93f8674b441b191f9763462a383 FileCreationInformation.ps1 %}

I was using and still am using the following function for most of my jobs, so i'll include it here for reference. It shows another way of doing the upload. This way requires more steps, but also gives you some more options for interacting with the folder. I don't really need to do that so I moved to the method up top. I didn't include the above setup as a function because i just wrote it into a deploy script for pushing some code updates, and i haven't adopted it for my standard jobs that copy data out to SPO, but i think you can see the differences anyway.

{% gist ef24c93f8674b441b191f9763462a383 SaveBinaryDirect.ps1 %}

Hopefully this helps someone, or at least helps maybe provide some additional information on why someone else's code isn't working. This was kind of bugging me for several months as an unresolved question for why I couldn't get the top method to work before. Basically it was just a combination of:

- my not knowing what I needed to do specifically on the destination path
- getting seemingly permissions errors back on various attempts to upload on the 'wrong' path
