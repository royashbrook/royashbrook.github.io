---
layout: post
title: dsquery, dsget, etc
---

how i have lived this long in my life without familiarity with these commands is beyond me. i'm a big fan of the command line especially commands that are already or mostly built in. these come with the admin pack. so much easier than opening up the ad user group mgr and other gui tools. here's a sample command.

``` ps1
dsquery group -name "any domain group in your domain" |
    dsget group -members |
    dsget user -email -samid -display
```

this command will pipe the full name to the get command to list the members which pipes to the command to show the email, samid, and the display name. obviously tons of different ways to do everything, but this is something i have been using a lot as normally when i open the ad tools it is to get similar simple details, not to do things that that tool really excels at. to me anyway.

``` ps1
dsquery user -samid "domainid" |
    dsget user -memberof |
    dsget group -samid
```

list group memberships for a user using the group samid.
