---
layout: post
title: optional sql parameters
---

if you have optional parameters in a stored procedure, this is a better way to do the comparison. by optional, i mean they will either be set to null, or will be null because they are not passed in or are otherwise not set. of course, realize indexing can affect performance as well as many other factors. you should always test. generally, this will be faster time wise. occasionally, i have found coalesce to incur more cpu time, but that is normally repaid by a faster turn around time as it seems to eliminate more rows quickly in the plan.

this is a general rule, not a golden one, so i'll re-iterate again that you should test all optimizations.

i've included the commands so you can look at the execution plans, io, and time analysis. for simplistic seperation, i will term the generally slower practice as 'bad' and the faster one as 'good'. in my example below @v and @c are varchar(50) and @a is a char(1). feel free to change everything, the case for coalesce generally holds true across the board.

{% gist 5ce903921a27a2141356fe54fee28372 %}

i also found several other good articles about this. some of differing opinion. most way better than this post. =P

http://blogs.x2line.com/al/archive/2004/03/01/189.aspx
http://sqljunkies.com/WebLog/amachanic/archive/2004/11/30/5311.aspx
http://weblogs.sqlteam.com/mladenp/articles/2937.aspx

my post is really only about examining the case for optional parameters or null values. some of these are about isnull vs coalesce. i'm a fan of coalesce personally.

### edit/comment
>> to pull nulls as well, you can just wrap it with another coalesce. like this:
>> ` select * from t where coalesce(f1,'') = coalesce(@v,f1,'') `
>> i didn't include this cuz... well quite frankly i didn't know anyone really would ever notice =P
>> using the fall through case statements is definitely an option. i've used it in the past, but typically i have tried to simplify the logic as much as possible having a trade off between simplicity and performance, else i reap the pains later after someone has 'patched' something =P
>> i supposed you could make that argument in either case. i will definitely try and make a point to test the methods you have listed the next time i'm working on a query with some optional params. when i do, i will try and post some results here.