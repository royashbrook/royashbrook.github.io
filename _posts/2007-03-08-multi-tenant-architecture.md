---
layout: post
title: multi-tenant architecture
---

Dave sent me the following link to examine. http://msdn2.microsoft.com/en-us/library/aa479086.aspx

Take a moment to peruse it and see what you think. We currently use this in some apps and I’ve seen this strategy in place in the past as well. In general, I personally haven't seen a place where it's *really* needed. The mixture of all of the data together could normally be solved by the much simpler solution of copying the data structure for each party. If you have common tables, some simple tell could be done or a shared database could be setup. I think it *seems* simpler because you can program common items for each separate global identity to use, but in the end I don't think you normally end up with that. Either you end up with widely generalized methods of retrieving data where everyone gets everything, or else you write completely custom stuff for everyone. Or you write generalized items that are copied for each separation that you can change if you need to. Then what happens if you need to apply a simple optimization to one thing, you have to find all of the points of separation and modify them as well. We all know that in the real world, you normally don't have your best programmers on break/fix. Often problems appear localized, but they cause minor global affects anyway. There are so many or, or, or and if, if, if statements I could append, that I get tired just thinking about the monotony of typing them all.

I think the business entity in this document is very rare. Normally it projects are plagued with bad practices, bad code, and bad management. Keeping things as simple as possible is the best base to start with. You can always expand from a simple base, but condensing from a more complex architecture always seems to lead to problems.

Am I saying this could never work or it's a ridiculous idea, of course not? I am saying that in the environments I’ve been in, I have rarely seen an organization that runs in a well and fully managed way. Due to that fact, the amount of complexity that a multi-tenant architecture introduces would lead me to recommend against it in almost every big business situation. In small businesses and small teams, I think it could work out pretty well though.

Perhaps the breakdown is mainly a communication and management problem.