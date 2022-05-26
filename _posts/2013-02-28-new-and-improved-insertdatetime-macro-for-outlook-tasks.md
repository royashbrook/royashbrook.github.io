---
layout: post
title: New and Improved InsertDateTime Macro for Outlook Tasks
---

In the past I blogged about a macro I use in outlook to automatically add a timedate stamp and my name to an outlook task to make updates easier to facilitate and read. I have updated that macro a little bit since that time and wanted to post the one I currently use. There are really only 2 major changes:

- Color Coding
- Insert at the beginning.

At my last job I used a sharepoint task list to manage most of my teams activities for quite some time. One of the guys on my team decided to add color coding to his macro and we all adopted it so we could see the different folks who edited a shared task at a glance. Typically the tasks weren’t passed around too much, but this was helpful for the collaborative tasks. I added this to my normal macro now and have also added a separate color for the Date and switch to the default for the delimiters. I also learned over time that adding a new line to the end wasn’t terribly helpful to me as I wanted to see the updates in reverse chronological order (latest updates first). This was particularly important once I began managing a team as I needed to be able to quickly see status on a task for someone on my team. So this macro goes to the beginning, then puts in a new line, then goes back to the beginning.

Here is an updated version of that macro:

{% gist 96b430a32b30f6082a568df813b1f800 %}

Note that there are some ways to get the timezone, but they all added a *lot* of extra code so I just put it in myself.  I generally just stick with whatever the default font is, but you can change that too if you wish. The code above essentially adds the line shown below to the top of the task. Sometimes I have had this hot-keyed, but generally I just create a button for the macro.

<span style="color:blue;font-weight:bold;">2013-02-27 23:45:50 EST</span> | <span style="color:red;font-weight:bold;">Roy Ashbrook<span> |
