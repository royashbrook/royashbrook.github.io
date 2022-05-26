---
layout: post
title: How to create and close TFS 2010 tasks in one step using excel 2010
---

So I use TFS 2010 at work for source control. My team also uses it for task management. Every day we put in everything that we did that day. It's not a big deal for us if the times are correct, but we just want to get the tasks created on the day they were put in so we can report on what we got done by day.

One of the gaps with using TFS in VS 2010 or with some of the products that integrate with it is that you *have* to open a task, and then close it. you can't just create a task called 'talked on phone to person x about subject y' mark it as 15 minutes of time and then close it. you have to create the task, then save it, then close it. not a big deal, but ultimately a bit of a pain every time you record work. Any pain during task tracking is, IMO, a real barrier to entry. It's already difficult enough to get people to record their tasks. =P

Anyway, on to the code. I wrote a macro that uses a list in excel hooked up to TFS and it allows you to just put in your tasks and then update some data row by row based on what you have put in and then it 'saves, closes, saves' for you. i wasn't able to find an example of how to do this on the web anywhere, but i found all the components i needed as seperate references. those references are listed below in the code.

{% gist 92ba4f6076b7f526f82ed7052bdf4c6f %}