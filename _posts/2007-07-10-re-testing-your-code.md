---
layout: post
title: Testing your code
---

<p>A colleague of mine asked me to comment on this post. <a href="http://blog.madskristensen.dk/post/Testing-your-code.aspx">http://blog.madskristensen.dk/post/Testing-your-code.aspx</a></p>
<p>For the duration of my post, I will assume that Mads is a "smart" guy, not a "stupid" guy. 😀</p>
<p>First, I suppose I should give my views of unit testing. If you are writing business logic code, I think you should write unit tests for it. Always. It's that simple. So, moving on. </p>
<blockquote>
<p>I'm very ambivalent about unit testing and always have been for many different reasons. Although testing is very important, I often find unit testing to be a time consuming liability but it depends on the project. </p>
</blockquote>
<p>When smart people post about stuff, stupid people read it and if they have no viewpoint will often take on that person's viewpoint. Since stupid programmers do far more damage in the world than smart ones, I'd rather the stupid ones wrote unit tests too. Of course, you could make the argument that stupid people would write stupid tests. This is true, but I think that the act of writing the tests would at least enforce a positive habit in their codewriting and would highlight far more of their errors over time. That is the entire point of TDD.</p>
<blockquote>
<p>Over the years I've build many helper class libraries which is used in many projects and can be considered as business independent fundamental classes. All the classes in those libraries are static and therefore cannot be considered to be entities, but merely a logical placeholder for static methods that works independently. </p>
<p>To unit test such libraries is a must have. They are simple to write and maintain and unit testing is important because the libraries are used in a vast amount of different projects. </p>
</blockquote>
<p>Is it so much harder to write tests for non-static classes? It hasn't proven so for me.</p>
<blockquote>
<p>A unit is the smallest testable part of an application which means that for object orientated applications/libraries it will always be a class or business entity. Of course you can test the individual methods, functions and constructors but it doesn't make much sense since the business entity is to be considered as a single unit and also works as such in real scenarios. </p>
</blockquote>
<p>I count basic business object CRUD stuff as non business logic. So I agree with him.</p>
<blockquote>
<p>For me it makes more sense to conduct use-case testing on business entities. It works much like unit testing, but is constructed so they test a complete flow of operations on the class. For instance, if I had a class called <em>Dog</em>, then I would write a use-case test that simulated how a real person would use the class. First <em>Feed</em> the <em>Dog</em>, then <em>Pet</em> it and then <em>Walk</em> it before it needs to <em>Sleep</em>. </p>
</blockquote>
<p>I agree that you should have scenario testing. If you consider the business object to be the smallest unit, and you are testing a complex scenario, you are still doing what many consider to be unit testing today. Perhaps there's an academic debate to be had over the definition of the term 'unit' in unit testing. 😛 I personally consider complex use-case style scenario tests to be unit tests, albeit more complex ones. I think the phrasing in his response here is damaging to the unknowing developer because they would probably see a negative towards 'unit testing' and a positive towards 'use-case testing'. I think a developer who decides to follow this paragraph could easily say to another one "I don't believe in unit testing. I only believe in use-case testing." To me, they are the same thing and applying a negative towards unit testing ("...it makes more sense...") isn't productive.</p>
<blockquote>
<p>For that purpose it doesn't really matter if you use unit testing frameworks such as <a href="http://en.wikipedia.org/wiki/XUnit">xUnit</a> or write it in a console application in the same Visual Studio solution. I like both approaches equally, but most often write a console application because I find it simpler. </p>
</blockquote>
<p>I think the testing harness used is a whole different religious debate 😛</p>
<blockquote>
<p>Financial applications used by banks or government applications needs to be tested extremely well. Those types of applications deal with people's personal data and money and must never ever fail. The same goes for applications that ships on CD's or DVD's unless they are updated automatically over the Internet. </p>
<p>There needs to be automatic tests running every day and both unit testing and use-case testing is very important. The 80/20 rule does not apply here; it needs to be as close to 100% code coverage as possible and each class needs to be use-case tested for dozens of scenarios. </p>
<p>To test this properly you need professional testers and QA's because the test is as important as the application itself. </p>
</blockquote>
<p>I would definitely agree here. </p>
<blockquote>
<p>The most common thing is to have no test but the application itself. Admit it; you do this all the time as well. Is it a dumb idea to use the application to test its dependent libraries and itself and not doing unit or use-case testing? No it definitely is not. The end user application/website is the ultimate test, but there is a serious problem with not having an automated test harness. If an error occurs you can spend more time finding the problem than it takes to fix it. For smaller projects though, I find it perfectly acceptable to test from the GUI without a test harness. </p>
<p>Also, it is very difficult to test GUI's automatically so it will be a good idea in most cases to use a test harness for every project in the application stack and then create a procedure for testing the actual GUI. In most cases it has to be done by humans manually.</p>
</blockquote>
<p>This is where I would play the 'seperate your logic from your presentation' card. If your presentation is seperate from your business logic, you can easily write unit tests that invoke the same business logic processes. In fact, you can even account for timing issues etc. if you know a user is going to click on a form and then stare at it for 15 minutes, you can re-produce that in a unit test. This is where your longer unit tests would be. I think he is saying that human testing should be enough in many instances. In my opinion, user acceptance testing is the realm for formal GUI testing, and the developer should have already written tests that can simulate load testing on his application before that.</p>
<blockquote>
<p><a href="http://en.wikipedia.org/wiki/Test-driven_development">TDD</a> is one of the hyped methodologies that I never really understood. I can't get my head around writing the test before the class it tests. In C# it doesn't make sense to me, because if I write a test to call a non-existing class and its methods and properties, then it will not compile. Then when I write the actual class I often compile to check for errors. That doesn't work either, because the test references some methods I haven't written yet and as so cannot compile. </p>
<p>Besides, in most cases I don't know 100% what properties and methods the class needs and which of them is public or private. Then I need to get back and change the test and thereby looses the point of writing the test before the class.</p>
</blockquote>
<p>I like TDD as it forces you to write tests. We're all naturally lazy and don't want to do that extra work. I would say on this that knocking something that helps promote better code through testing is probably not the best thing to do. Especially if you are one of the smart people. A stupid developer will read this and think they don't need to use TDD when they probably could have saved the world a lot of pain and agony by not having to rewrite their untested code if they did use it.  </p>
<blockquote>
<p>For smaller projects with few dependencies <a href="http://www.userdriven.org/about-user-driven">user driven development</a> is for me the best way to test. Consider a rather small project like <a href="http://www.dotnetblogengine.net/">BlogEngine.NET</a> with one web project and one class library. The class library is only used by the web project and nowhere else. That means that the class library has no classes, methods or properties that is not used by the web project so by testing the web project, the class library get's 100% code coverage. </p>
</blockquote>
<p>I don't know what this has to do with unit testing. I suppose it's here as a contrast to TDD previously mentioned. I think you can be user driven, and test driven at the same time.</p>
<blockquote>
<p>If you always make sure your code is simple, clean and refactored, then testing is equally simple to write. Complex code needs complex testing and the maintenance of the tests will increase with every change to the code. Every time you change a little thing, you need to rewrite many tests if your code is complex. That goes for both unit testing and use-case testing. </p>
</blockquote>
<p>I think this is part of the point of writing the unit tests up front. It will help to keep your code as simple as possible.</p>
<blockquote>
<p>I find unit testing to be good for very few types of projects - helper class libraries and never-fail-applications. User driven development with use-case testing is my absolute favourite. I don't see the big difference in using a xUnit framework or using a console application, because you only get the benefit from a test project when something fails. For automatic testing though, you need a testing framework that logs the results. </p>
</blockquote>
<p>I think I agree with a lot of the things that are said, just not with the conclusions drawn. I think *not* using unit testing should be rare thing, not the norm. I think a smart person in a position of authority shouldn't advocate skipping unit testing because people that already suck will use that opinion to enable them to continue sucking. Unit testing could help alleviate some of the suckage. 😀</p>
<p>I'd like to note that I like Mads' blog. I've found many useful and well written posts on his blog (some favs: <a class="" mce_href="http://blog.madskristensen.dk/post/Don%e2%80%99t-use-the-ThreadPool-in-ASP.NET.aspx" href="http://blog.madskristensen.dk/post/Don%e2%80%99t-use-the-ThreadPool-in-ASP.NET.aspx">1</a>, <a class="" mce_href="http://blog.madskristensen.dk/post/Implement-micro-formats-on-your-website.aspx" href="http://blog.madskristensen.dk/post/Implement-micro-formats-on-your-website.aspx">2</a>, <a class="" mce_href="http://blog.madskristensen.dk/post/Life-without-TV-%e2%80%93-is-it-possible.aspx" href="http://blog.madskristensen.dk/post/Life-without-TV-%e2%80%93-is-it-possible.aspx">3</a>) including this one. I just happen to disagree with his conclusion this time.</p>