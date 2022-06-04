---
layout: post
published: true
title: query installed software from remote machine registry
---
I don't do this all the time, but it _is_ something I need to do frequently enough that it warrants saving.

Here's how you can do it with get-itemproperty and invoke-command. 

```powershell
$c = 'machinename'
$s = {
    Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*
}
icm -ComputerName $c -ScriptBlock $s |
    select PSComputerName, DisplayName, DisplayVersion, Publisher, InstallDate |
    ft –AutoSize
```

The problem with this is that you have to be able to call powershell on the remote machine and and a host of other things. This can be great, but I find I usually just use reg query and parse the results. Like so:

```powershell
$c = "machine"
$k = "\\$c\hklm\software\microsoft\windows\currentversion\uninstall"
$v = "    DisplayName    REG_SZ    "
(reg query $k /s) -match $v -replace $v | sort -Unique
```

I feel like I have had to tweak this at some point in the past as I usually am just calling the reg query and maybe copy/pasting it, but with a little powershell we can clean it up. It becomes messier if you need to get the versions, etc, but for a quick and dirty look, reg query for just the displayname normally gets me where I need to go.


