---
layout: post
title: partial trust, permcalc, and caspol, OH MY!
---

System.Security.SecurityExceptions will always be … well some kind of security problem ;)

Running .net exes over the network requires different .net perms. You can see the perms an assembly requires using permcalc from the .net sdk. It used to be called… I think permview.

You can run caspol –rs[g/p] %path to assembly% to see some info on the permissions. G will show you the groups it belongs to in the gac, P will list the permissions.

``` plaintext
From: xxxxxxxxxxxxxxx
Sent: Thursday, July 17, 2008 5:40 PM
To: Ashbrook, Roy
Subject: question

Whats up roy… any idea why I’m getting this error xxxxxxxxxxxx

Microsoft Windows XP [Version 5.1.2600]
(C) Copyright 1985-2001 Microsoft Corp.

C:\WINNT>\\server\dotnetapp.exe

Unhandled Exception: System.Security.SecurityException: That assembly does not allow partially trusted callers.
at System.Security.CodeAccessSecurityEngine.ThrowSecurityException(Assembly asm, PermissionSet granted, PermissionSet refused, RuntimeMethodHandle rmh, SecurityAction action, Object demand, IPermission permThatFailed) at....
```