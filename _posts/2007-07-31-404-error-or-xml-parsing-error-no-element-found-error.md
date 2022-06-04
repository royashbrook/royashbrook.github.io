---
layout: post
title: 404 error or XML Parsing Error - no element found error on IIS with .Net
---

 A colleague asked me to be a second pair of eyes for a project he was working on that was getting this error. He had just deployed a new .NET web site to a relatively clean IIS6 machine. He was receiving a 404 error in IE when he merely tried to view his asmx pages. When I tried to open them in Firefox, I received an XML Parsing Error: no element found error. After some looking on the web, I didn't find anything that seemed pertinent to our situation. So, I just started looking around. I noticed he had ASP.NET set to run as 1.1 in the web site. Changing this to 2.0 fixed the problem. Hope that helps someone else! 😀
