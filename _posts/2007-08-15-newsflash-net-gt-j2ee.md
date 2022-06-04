---
layout: post
title: newsflash .NET > J2EE
---

<p>I was surfing around today looking for information on .NET vs J2EE performance when I came across this site:</p>
<p><a mce_href="http://msdn2.microsoft.com/en-us/netframework/bb499684.aspx" href="http://msdn2.microsoft.com/en-us/netframework/bb499684.aspx">http://msdn2.microsoft.com/en-us/netframework/bb499684.aspx</a></p>
<p>Summary from that site:</p>
<blockquote>
<p>"This application is an end-to-end sample application for .NET Enterprise Application Server technologies. It is a service-oriented application based on Windows Communication Foundation (.NET 3.0) and ASP.NET, and illustrates many of the .NET enterprise development technologies for building highly scalable, rich "enterprise-connected" applications. It is designed as a benchmark kit to illustrate alternative technologies within .NET and their relative performance.</p>
<p>The application offers full interoperability with J2EE and <a onclick="javascript:Track('ctl00_ctl01|ctl00_ctl02',this);" href="http://go.microsoft.com/?linkid=6895278">IBM WebSphere's Trade 6.1</a> sample application. As such, the application offers an excellent opportunity for developers to learn about .NET and building interoperable, service-oriented applications."</p>
</blockquote>
<p> <br />
Oh, ho, ho! Daily, I have to listen to drivel about IBM's supposed superiority over .NET, which I have often found extremely hard to believe given the quality of applications I have seen generally produced by JAVA shops. This isn't a knock against JAVA devs or JAVA as a whole, I can only know what I have seen. I haven't seen anything "superior" ever from JAVA over .NET. I haven't seen anything I considered inferior either... until now. 😀</p>
<p>Check the short numbers snagged from <a mce_href="http://hyperthink.net/blog/2007/08/10/net%20stocktrader%20wcf%20vs%20websphere.aspx" href="http://hyperthink.net/blog/2007/08/10/net%20stocktrader%20wcf%20vs%20websphere.aspx">here</a>:</p>
<ul>
    <li>.NET 3.0 hosted on IIS with an Http binding and XML encoding offers <strong>124% better throughput </strong>than the fastest WebSphere/EJB Web Service implementation tested; and 46% better throughput than the JDBC (no entity beans) WebSphere implementation tested. </li>
    <li>.NET 3.0 self-hosted over Http/XML <strong>offers 225% better throughput </strong>than the fastest WebSphere/EJB Web Service implementation tested; and 113% better throughput than the JDBC WebSphere implementation tested. </li>
    <li>.NET 3.0 with binary encoding over a TCP binding <strong>offers 488% better throughput </strong>than the fastest WebSphere EJB Web service implementation tested; and 284% better throughput than the JDBC WebSphere implementation tested. </li>
</ul>
<p>Here's an eWeek article on the subject:</p>
<p><a mce_href="http://www.eweek.com/article2/0,1895,2150723,00.asp" href="http://www.eweek.com/article2/0,1895,2150723,00.asp">http://www.eweek.com/article2/0,1895,2150723,00.asp</a></p>
<p> <br />
Some highlights:</p>
<blockquote>
<p>"The benchmark compares .Net StockTrader against IBM's IBM Trade 6.1 performance application. Trade 6.1 is a J2EE (Java 2 Platform, Enterprise Edition)-based application IBM, of Armonk, N.Y., uses as a best-practice performance sample application and capacity testing tool for IBM WebSphere 6.1. The application is available for free and can be downloaded from the IBM WebSphere performance site."</p>
<p>"Also, because both the J2EE-based Trade 6.1 and .Net-based StockTrader applications expose their middle-tier services as industry-standard Web services, the two applications can be integrated with no code changes required, Leake said."</p>
<p>"'We did a cost-per-transactions analysis, and we came in between one-half and one-fifth of the cost" of IBM's solution, Leake said."'</p>
</blockquote>
<p>And InfoWorld:</p>
<p><a mce_href="http://weblog.infoworld.com/yager/archives/2007/06/benchmarking_j2.html" href="http://weblog.infoworld.com/yager/archives/2007/06/benchmarking_j2.html">http://weblog.infoworld.com/yager/archives/2007/06/benchmarking_j2.html</a><br />
 </p>
<p>highlights:</p>
<blockquote>
<p>"<span class="artText">Knowing that any result published by Microsoft would, at best, be greeted with skepticism, Greg took a unique path to disarming the question of trust. He decided to adopt what may be the toughest, most realistic, best-documented benchmark in all of J2EE-dom, IBM's Trade stock market simulator, as his model. IBM uses Trade to show off the scalability of WebSphere in massive deployments."</span></p>
<p>"He chose Trade and immediately became obsessed with making sure that Java had full home-court advantage.</p>
<p>Greg showed me the thousands of pages of IBM documentation of Trade and WebSphere that he had to chew through in order to understand Trade well enough to port it. And it was a port. He didn't derive a functional specification from Trade's behavior and write a .Net app that looked like Trade. He ported it, line by line, because he was not only building a benchmark but a proof case for the feasibility of porting a large Java code base and Web front end to C# and IIS (Internet Information Services)."</p>
<p>"<span class="artText">In the end, Trade.net kicks Trade all the way around the block. As I said at the start of this story, that's what vendor-published benchmarks are supposed to do, and I'm sure that if .Net trailed Java, I'd never have been invited to Redmond to see Greg Leake's work."</span></p>
</blockquote>
<p> </p>
<p>MS full paper: <a mce_href="http://download.microsoft.com/download/F/C/D/FCDDF8B6-AE97-4CDB-BEBC-FF22DA524A98/TradeBenchmark.pdf" href="http://download.microsoft.com/download/F/C/D/FCDDF8B6-AE97-4CDB-BEBC-FF22DA524A98/TradeBenchmark.pdf">http://download.microsoft.com/download/F/C/D/FCDDF8B6-AE97-4CDB-BEBC-FF22DA524A98/TradeBenchmark.pdf</a></p>
<p>Two blog posts that showed up in my post-location-research. 😛 </p>
<p><a mce_href="http://blogs.msdn.com/smcbreen/archive/2007/06/29/net-v-j2ee-sample-application.aspx" href="http://blogs.msdn.com/smcbreen/archive/2007/06/29/net-v-j2ee-sample-application.aspx">http://blogs.msdn.com/smcbreen/archive/2007/06/29/net-v-j2ee-sample-application.aspx</a></p>
<p><a mce_href="http://hyperthink.net/blog/2007/08/10/net%20stocktrader%20wcf%20vs%20websphere.aspx" href="http://hyperthink.net/blog/2007/08/10/net%20stocktrader%20wcf%20vs%20websphere.aspx">http://hyperthink.net/blog/2007/08/10/net%20stocktrader%20wcf%20vs%20websphere.aspx</a> </p>
<p> </p>
<p>So, to me, this just restates something I have always thought: IBM is all about selling the Hype of using IBM, not about selling technology that really performs best. I understand that there are a lot of other things you get with a contract with IBM, but I don't think I'll ever be convinced it's a better technical solution. I have dealt with IBM at quite a few companies as they are trying to land contracts and I have always found them to be more about winning over the decision makers with favors and promises, not about actually delivering a killer product. </p>
<p> </p>
<p> </p>

