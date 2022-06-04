---
layout: post
title: Top 20 .NET Garbage Collection (GC) Articles
---

Ah. Garbage Collection... how I love and hate thee. 😛

I think one sad thing about programming in .net is that it seems many developers don't know or care anything about garbage collection and memory management. You used to *have* to know about it in order to write bug free code. I suppose it is a two edge sword in that developers can develop faster as the intention was to relieve developers of the need to know or care anything about memory management. I personally have always tried to follow best practices for implementation and have tried to stay somewhat cautious of issues related to memory. I've been fortunate in never really having any memory management related issues in my previous applications. However, there, but for the grace of God, I too could go.

Recently, we have come across an issue that has required some intense scrutiny on our memory management process. Although it appears we are using mostly best practices for our access and use of certain objects (mainly in the System.Xml namespace) we are still running into memory problems. Unfortunately, our application includes some manual invocations of the GC.Collect() (EWWWWWWWW) to supposedly free up some of the memory consumed by some large objects.

Blah blah blah. The point is I have had to become a lot more familiar with garbage collection and memory management in general. I figured I would post what (I have found) to be the best sources for information on this topic, in the order I think it's best to read them. Some of these refer to each other and I am merely following the order suggested by them. Others, I am simply including the order that seemed to make the most sense to me. I am extremely confident that after you read these articles, you will have a great handle on memory management and how the garbage collector work in .net.

<ol>
    <li>
    <div mce_keep="true"><a class="" title="Garbage Collection: Automatic Memory Management in the Microsoft .NET Framework" mce_href="http://msdn.microsoft.com/msdnmag/issues/1100/GCI/default.aspx" href="http://msdn.microsoft.com/msdnmag/issues/1100/GCI/default.aspx">Garbage Collection: Automatic Memory Management in the Microsoft .NET Framework</a></div>
    </li>
    <li>
    <div mce_keep="true"><font color="#810081"><a class="" title="Garbage Collection-Part 2: Automatic Memory Management in the Microsoft .NET Framework" mce_href="http://msdn.microsoft.com/msdnmag/issues/1200/GCI2/" href="http://msdn.microsoft.com/msdnmag/issues/1200/GCI2/">Garbage Collection-Part 2: Automatic Memory Management in the Microsoft .NET Framework</a></font></div>
    </li>
    <li>
    <div mce_keep="true"><a class="" title="C# Heap(ing) Vs Stack(ing) in .NET: Part I  " mce_href="http://www.c-sharpcorner.com/UploadFile/rmcochran/csharp_memory01122006130034PM/csharp_memory.aspx?ArticleID=9adb0e3c-b3f6-40b5-98b5-413b6d348b91" href="http://www.c-sharpcorner.com/UploadFile/rmcochran/csharp_memory01122006130034PM/csharp_memory.aspx?ArticleID=9adb0e3c-b3f6-40b5-98b5-413b6d348b91">C# Heap(ing) Vs Stack(ing) in .NET: Part I</a></div>
    </li>
    <li>
    <div mce_keep="true"><a class="" title="C# Heap(ing) Vs Stack(ing) in .NET: Part II" mce_href="http://www.c-sharpcorner.com/UploadFile/rmcochran/csharp_memory2B01142006125918PM/csharp_memory2B.aspx" href="http://www.c-sharpcorner.com/UploadFile/rmcochran/csharp_memory2B01142006125918PM/csharp_memory2B.aspx">C# Heap(ing) Vs Stack(ing) in .NET: Part II</a></div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="C# Heap(ing) Vs Stack(ing) in .NET: Part III" mce_href="http://www.c-sharpcorner.com/UploadFile/rmcochran/chsarp_memory401152006094206AM/chsarp_memory4.aspx" href="http://www.c-sharpcorner.com/UploadFile/rmcochran/chsarp_memory401152006094206AM/chsarp_memory4.aspx">C# Heap(ing) Vs Stack(ing) in .NET: Part III</a></div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="C# Heap(ing) Vs Stack(ing) in .NET: Part IV" mce_href="http://www.c-sharpcorner.com/UploadFile/rmcochran/csharp_memory_401282006141834PM/csharp_memory_4.aspx" href="http://www.c-sharpcorner.com/UploadFile/rmcochran/csharp_memory_401282006141834PM/csharp_memory_4.aspx">C# Heap(ing) Vs Stack(ing) in .NET: Part IV</a></div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="Using GC Efficiently - Part 1" mce_href="http://blogs.msdn.com/maoni/archive/2004/06/15/156626.aspx" href="http://blogs.msdn.com/maoni/archive/2004/06/15/156626.aspx">Using GC Efficiently - Part 1</a></div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="Using GC Efficiently - Part 2" mce_href="http://blogs.msdn.com/maoni/archive/2004/09/25/234273.aspx" href="http://blogs.msdn.com/maoni/archive/2004/09/25/234273.aspx">Using GC Efficiently - Part 2</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a href="http://blogs.msdn.com/maoni/archive/2004/11/04/252697.aspx">Clearing up some confusion over finalization and other areas in GC</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="Using GC Efficiently - Part 3" mce_href="http://blogs.msdn.com/maoni/archive/2004/12/19/327149.aspx" href="http://blogs.msdn.com/maoni/archive/2004/12/19/327149.aspx">Using GC Efficiently - Part 3</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="Using GC Efficiently - Part 4" mce_href="http://blogs.msdn.com/maoni/archive/2005/05/06/415296.aspx" href="http://blogs.msdn.com/maoni/archive/2005/05/06/415296.aspx">Using GC Efficiently - Part 4</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="64-bit vs 32-bit" mce_href="http://blogs.msdn.com/maoni/archive/2007/05/15/64-bit-vs-32-bit.aspx" href="http://blogs.msdn.com/maoni/archive/2007/05/15/64-bit-vs-32-bit.aspx">64-bit vs 32-bit</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="When memory is running low..." mce_href="http://blogs.msdn.com/maoni/archive/2006/06/06/when-memory-is-running-low.aspx" href="http://blogs.msdn.com/maoni/archive/2006/06/06/when-memory-is-running-low.aspx">When memory is running low...</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="ASP.NET Case Study: High CPU in GC - Large objects and high allocation rates" mce_href="http://blogs.msdn.com/tess/archive/2006/06/22/643309.aspx" href="http://blogs.msdn.com/tess/archive/2006/06/22/643309.aspx">ASP.NET Case Study: High CPU in GC - Large objects and high allocation rates</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="Two things to avoid for better memory usage" mce_href="http://blogs.msdn.com/ricom/archive/2003/12/02/40780.aspx" href="http://blogs.msdn.com/ricom/archive/2003/12/02/40780.aspx">Two things to avoid for better memory usage</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="Garbage Collection with Jeffrey Richter" mce_href="http://blogs.msdn.com/geekspeak/archive/2006/11/03/resources-for-geekspeak-garbage-collection-with-jeffrey-richter.aspx" href="http://blogs.msdn.com/geekspeak/archive/2006/11/03/resources-for-geekspeak-garbage-collection-with-jeffrey-richter.aspx">Garbage Collection with Jeffrey Richter</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title=".Net Garbage Collector PopQuiz Followup" mce_href="http://blogs.msdn.com/tess/archive/2007/04/10/net-garbage-collector-popquiz-followup.aspx" href="http://blogs.msdn.com/tess/archive/2007/04/10/net-garbage-collector-popquiz-followup.aspx">.NET Garbage Collector PopQuiz Followup</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="Mid-life Crisis " mce_href="http://blogs.msdn.com/ricom/archive/2003/12/04/41281.aspx" href="http://blogs.msdn.com/ricom/archive/2003/12/04/41281.aspx">Mid-life Crisis</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a class="" title="Garbage Collector Basics and Performance Hints" mce_href="http://msdn2.microsoft.com/en-us/library/ms973837.aspx" href="http://msdn2.microsoft.com/en-us/library/ms973837.aspx">Garbage Collector Basics and Performance Hints</a></div>
    </div>
    </div>
    </li>
    <li>
    <div mce_keep="true">
    <div mce_keep="true">
    <div mce_keep="true"><a href="http://blogs.msdn.com/johan/archive/2007/04/20/memory-management-in-the-net-framework.aspx">Memory Management in the .NET Framework.aspx</a>  </div>
    </div>
    </div>
    </li>
</ol>

I'm also including, as a bonus, [this article](http://www.bluebytesoftware.com/blog/PermaLink.aspx?guid=88e62cdf-5919-4ac7-bc33-20c06ae539ae) which goes very deep into resource management using dispose and finalize. I went ahead and tagged this as architecture as well since I believe any good architect should want to know about the underlying framework.
 
Enjoy!
 
[edit] I'm going to tack a [+1](http://www.codeproject.com/dotnet/garbagecollection.asp) onto this stack because I forgot I read this one somewhere between 1 and 6 and I found it helpful. If you read the above though, it's not really necessary.
