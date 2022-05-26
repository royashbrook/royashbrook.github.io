---
layout: post
title: AppHarbor and dealing with annoying firewall rules. =P
---

A few days ago I was reading one of my usual news feeds [The Daily WTF](http://thedailywtf.com/) and saw [Alex](http://thedailywtf.com/Authors/Alex_Papadimoulis.aspx)’s post [(A (Long Overdue) BuildMaster Introduction)](http://thedailywtf.com/Articles/A-(Long-Overdue)-BuildMaster-Introduction.aspx) on their BuildMaster product. It looked pretty cool so I downloaded it myself and forwarded it to a colleague at another place of employment. Unfortunately for that individual their firewall policy prevents downloading .EXE files so they could not download any of the installs as they were all .EXE files.

So firstly, I would recommend that anyone that wants to have their cool/nifty product downloaded by someone in a large corp should at least *offer* the option of getting a compressed version that is not an MSI or an EXE file. I’ve seen these blocked at a number of institutions and while they are blocked for good reason, if you want to start a guerilla campaign with developers, it’s easier if they can get your stuff.

Secondly, to circumvent this PITA, I present __DEX__ which stands for 'download EXE'. =) If you are one of the frustrated folks who can’t download EXE files, feel free to use this 'new' and 'innovative' service =P. You will probably want to let me know as I may change the key from time to time.

It’s very simple.

- Here is the URL format - http://royashbrook.apphb.com/dex.ashx?k=[key]&u=[urltoexe]
- It just processes the url string looking for the values of k,u, and one not shown here gzs.
- k must be YaTl7a4akBMefeCZ currently. It’s just a random key now and will change from time to time. I copied it from the text of a password generated here. This is where I remind you again to just drop me a line if you are using it.
- u should be the url to the exe file you want WITHOUT THE .EXE AT THE END.
- The service will then call 'u' with an added .EXE at the end and stream it down to you as renameme.txt.
- If you include gzs=1 it will gzip the stream and try and friendly name it for you although that doesn’t work all the time.

Here are some sample URLs:

- BuildMaster
    - http://royashbrook.apphb.com/dex.ashx?k=YaTl7a4akBMefeCZ&u=http://inedo.com/files/buildmaster/sql/3.5.8
- Some Project on Google Code
    - http://royashbrook.apphb.com/dex.ashx?k=YaTl7a4akBMefeCZ&u=https://pirate.googlecode.com/files/Pirate-1008
- Old Version of LINQPad
    - http://royashbrook.apphb.com/dex.ashx?k=YaTl7a4akBMefeCZ&u=http://www.linqpad.net/GetFile.aspx?LINQPad

Codeplex project is at http://dex.codeplex.com/.
