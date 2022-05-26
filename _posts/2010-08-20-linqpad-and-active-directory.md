---
layout: post
title: LINQPad and Active Directory
---

I am a huge fan of LINQPad.

Something on my wish-list, as far as my own personal knowledge, for a longtime with it was how to query Active Directory information when I needed it. This was more for convenience because quite often I need to look up some AD info when I am looking at a scrub for some data regarding a user. Normally I just net use or dsquery/dsget etc. The key is System.DirectoryServices.AccountManagement. Note this is a .NET 3.5 and 4.0 only library so earlier than that, sorry. You aren't out of luck, but the other ways seem to be less cumbersome to me than this very easy way. Be mindful of the fact that performing a .Dump() on one of these objects will produce a TON of stuff. So doing something like User.GetGroups().Dump() is probably a Very Bad IdeaTM. Also, don't forget that since we're using LINQ to Objects, you will have case sensitivity issues unless you deal with it yourself.

1. Edit the Query Properties in LINQPad:
![img](http://www.drowningintechnicaldebt.com/images/www_drowningintechnicaldebt_com/royashbrook/082010_2006_LINQPadandA1.png)

2. Add a reference to System.DirectoryServices.AccountManagement.dll:
![img](http://www.drowningintechnicaldebt.com/images/www_drowningintechnicaldebt_com/royashbrook/082010_2006_LINQPadandA2.png)

3. Update the Additional Namespace Imports tab to reflect the namespace you want to import:
![img](http://www.drowningintechnicaldebt.com/images/www_drowningintechnicaldebt_com/royashbrook/082010_2006_LINQPadandA3.png)

4. PROFIT!! =P
```csharp
using(PrincipalContext ctx = new PrincipalContext(ContextType.Domain, "MyDomain))
    using(UserPrincipal usr = UserPrincipal.FindByIdentity(ctx, IdentityType.SamAccountName, "MyUserID"))
        usr.Dump();
```