---
layout: post
published: true
title: Batch decode user agent strings
subtitle: mozilla who/what now??
date: '2020-06-18'
image: /img/secretagent.png
---
Recently I downloaded some web logs for an app I have running. This app is pretty old and hasn't been changed in quite some time, but it was tweaked a year or so ago in order to get it running as an azure app service. We're looking to freshen it up a bit, but it is a fairly simple app and there are no requests to 'change' it so we want to be careful not to break anything for any of the users.

To that end, I've been making a list of the changes, bugs, etc. Basically anything that we may want to look at changing. One component we need to look at is browser compatibility as we want this to be transparent to the users if possible. And to make that happen we're checking our logs. So I'm going to write this up just in case anyone else needs to do it as I couldn't find a quick/easy way to convert this information.

Maybe this isn't the quickest or easiest, but it's where I landed and I couldn't find anything that explained how to do this in one place so hopefully it helps someone else. If not, hopefully it helps future me. =)

So now I have some iis weblogs downloaded from azure. These are in iis log format. Those logs and powershell are required to start. 

{% gist 63c8bdfb52cb9a1aa88624969badd720 extractAgentInfo.ps1 %}

So now I have my agents. I found plenty of places online where I could copy/paste in the agent string and get the info I wanted. But as I may have to do this for some other websites, I wanted to get something a little bit more reliable and repeatable than that. One of the sites I used to copy/paste while checking was [this one](whatismybrowser.com) and it also happens they have a free tier for an api that allows a limited number of queries. Since I really only need a very limited number of on demand runs of this, that works for me. So for the next part you'll need an api key from there so you can send a request in to get the info you may want. In my case, I'm really just looking for something like 'IE11 on Windows' or 'Chrome 70' or similar. I hadn't decided __exactly__ what I wanted to see, but definitely not the agent string itself. =)

Note:
- [API Documentation](https://developers.whatismybrowser.com/api/docs/v2/integration-guide/)
- I am using the "User Agent Parse" because you don't seem to get batch access with the free plan
- I'll be getting the full parse results back, but i'm really looking for the 'simple' name

So this is what I did next:

{% gist 63c8bdfb52cb9a1aa88624969badd720 convertUserAgentStringToSimpleName.ps1 %}

That's it. This got me what I needed personally so if you found this, I hope it works for you. I am not 100% sure if the `$results` object will end up having the same order **for sure** as the originally ordered hash, but you could write it all out to a file as you get the parse responses, or you could key it or return the results from iwr as you iterate over it or something else. This got me where I needed to go so I left it there.

Edit: Just in case you are wondering how to download the http logs, I wrote about it in [another article](http://ashbrook.io/2020-06-17-download-azure-webapp-appservice-logs-via-cli/) recently. But you can also go and download the log files via FTP using the info in the portal or use the vscode azure integration to download from that web app directly. I'm guessing there are other ways, but I liked the CLI so that's what I wrote about recently. =)

Edit, Edit: I decided to add one more detail in here about getting these numbers merged back in with counts. The gist below details taking the `$agentinfo` data and merging it with the friendly name info as well as totaling things up by the browser or the os with percentages which is what i ultimately wanted to do as well. I just went down top the operating system and browser with versions (so windows 8 or chrome 83) but you could pull out different user agent levels. say if you wanted to check for 'windows' vs 'ios' or 'chrome' vs 'edge' instead of including the versions. But this was enough for me as it told me that >99% of my users were on Windows of some sort and my users were about 60% Chrome with the remaining 40% being about evenly split with IE11 and Edge with <1% being something else.

{% gist 63c8bdfb52cb9a1aa88624969badd720 mergingBackWithNumbers.ps1 %}