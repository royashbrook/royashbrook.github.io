---
layout: post
title: Why "SET NOCOUNT ON" Sucks
---

As I mentioned in a previous post,  David Hayden sat in the front row and gave me an especially hard time about 'set nocount on' as a tip. I don't remember the exact words, so I'm going to have to roughly paraphrase. His complaint was that some data access layers or object relational mapping tools or other data abstraction methods in .NET sometimes utilized the return record count to determine success or failure. Michael Wells (also in the front row) stated that normally he just utilized a try/catch to determine success, or just relying on a thrown error. Typically, I have taken this approach as well. David was pretty adamant about this causing him massive problems in the past though. I must admit I was a bit skeptical that it was a problem because I *thought* I had checked prior on this. I said that I believed I went to check it once but never really found a good piece of code in our current code base to test it on. At the time, it was more of a 'it doesn't matter' for me and so I never went back to check it. However, faced with a challenge to what is typically a staple "SQL Tip" I had to verify or deny the claim. So, on the trip back I checked. Long story short, David is absolutely right.

Now, for a standard reader, there isn't a return value. We can check the HasRows property. This value is there regardless and that's pretty much to be expected. So the following code returns true (if there is data) whether or not the query utilizes SET NOCOUNT ON:

```csharp
using (SqlDataReader r = cm.ExecuteReader()) {
    return r.HasRows.ToString();
}
``` 
For execute scalar, obviously you can just check the result so there's no point in looking at that. For ExecuteNonQuery however, it definitely shows. The following code will print a number for the rows affected (0 if none are affected)  if SET NOCOUNT ON is *not* included, however if you include it, it will print a -1.

```csharp
using (SqlCommand cm = new SqlCommand(q, c)) {
    c.Open();
    Console.WriteLine(cm.ExecuteNonQuery().ToString());
    c.Close();
}
```
So, just to alleviate any confusion, if you are using some sort of data abstraction code that relies on the recordcount that is suppressed by utilizing SET NOCOUNT ON, don't use SET NOCOUNT ON. It will suck then. 😀 I would recommend strongly that for *any* performance enhancements you want to apply, you test and retest.

Hopefully no one takes this as my advocating against SET NOCOUNT ON, I am still a fan and use it almost all of the time. I personally have never had it cause me any pain, but for every enhancement, there is someone for whom it has caused a lot of pain, so please test accordingly.