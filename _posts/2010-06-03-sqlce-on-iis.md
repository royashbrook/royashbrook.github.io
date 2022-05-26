---
layout: post
title: SQLCE on IIS
---

So this apparently isn't supported, but I just thought I would jot down some notes about how to get it working. It is really not that hard as long as you don't mind installing SQLCE on your IIS server. I am still working on how to distribute the SQLCE files with the application.

I have a very small set of data that is mostly read and so I wanted to just dump it on a web server and not have a sql server. SQLCE seemed perfect for this although apparently that's not a use it's currently designed for. Here are some links I had open while I was working on this:

http://www.tanguay.info/web/index.php?pg=howtos&id=10
http://www.coderenaissance.com/2010/04/using-sqlce-with-entity-framework-and.html
there are like a billion other links. I simply setup the db in a project where sqlce was allowed, and then copied the classes into the other project. A little connection string massaging and it worked fine.

Anyway, none of that is new/original, I couldn't find anything really current regarding how to get the sqlce dataprovider working on iis. There were a lot of references to machine.config and web.config/app.config fixes to put the provider in there etc. None of these worked for me. What did work was:

Turning on the feature. I guess it is disabled by default. I simply added a line in the global.asax application start.
```csharp
protected void Application_Start(object sender, EventArgs e) {
    AppDomain.CurrentDomain.SetData("SQLServerCompactEditionUnderWebHosting", true);
}
```

Next, I (insert a long time of fiddling with the approaches above to get it to work but didn't) simply installed the sqlce runtime. I downloaded it from http://www.microsoft.com/downloads/details.aspx?familyid=E497988A-C93A-404C-B161-3A0B323DCE24&displaylang=en and installed the 32bit version. I am running .net 4.0, vs2010, windows server 2003 r2.

Hopefully I can find a way to distribute the SQLCE dlls with the app and it will just work since doing this install defeats one of my purposes which was to just simply be able to distribute a tiny set of data along with a simple access/manage set of services and not have to do any dependency installs. I have to say I think it's pretty stupid not to allow this to just work or to provide any clear instruction on microsoft's part. The realistic option for a normal person (and even for me) is to just install sqlite or do something else. I don't need full gui tool interoperability and I don't mind the hoops I have to jump through to get the classes setup etc, but I do wish if I had a project, any project, with a reference to that data provider that it would deploy whatever was/is needed easily. I'm sure there *is* a way to get it to work, but I haven't been able to find it. C'mon MS, toss me a token document that says 'if you want to use sqlce with anything, deploy these dlls, and do this in your solution and it'll work anywhere.' Or at least tell me where the data provider is weirded out on iis so I can fix it. I thought I was set when I found the info in step 1 above, but nope =P