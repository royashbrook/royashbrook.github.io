---
layout: post
title: create a custom sendto item to process files... with powershell...
---

in reply to dennis' comment on my last post on this little thing (http://drowningintechnicaldebt.com/blogs/royashbrook/archive/2009/01/21/create-a-custom-sendto-item-to-process-files.aspx), here's the way to do this with powershell

- make sure you have powershell installed and working
- create script somewhere with text: `foreach ($i in $args) {move-item $i $i.Replace(".mod", ".mpeg")}`
- create a shortcut in the sendto folder that has the command of `[pathtopowershell.exe] -noexit [pathtoscript]`
- enjoy!

i was only renaming certain files to certain other files (MOD to MPEG), but obviously you can customize this however you see fit. i had my batch before set not to exit so i could see what was up, but you could take out the -noexit if you wanted it to just run and bail. powershell definitely is more powerful, but IMO still a huge pain for most stuff i would use it for. if most admins invested the amount of time it would take to ramp up using powershell into learning existing tools, they probably wouldn't need it. i'm definitely enjoying playing with it though. here are some references i used:

http://blogs.msdn.com/mikeormond/archive/2008/01/20/powershell-batch-rename.aspx
http://blogs.msdn.com/powershell/
http://sandbox.manning.com/thread.jspa?messageID=62599
http://powershell.wik.is/Cmdlet_Help/Microsoft.PowerShell.Management/Rename-Item
http://adrianba.net/archive/2007/10/29/rename-item-and-square-brackets-in-powershell-no--literalpath.aspx
