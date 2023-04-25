---
layout: post
published: true
title: how to enable passwordless ssh on windows 2012 r2
date: '2022-07-27'
---

With a newer windows server, ssh is a [bit more turnkey](https://techcommunity.microsoft.com/t5/itops-talk-blog/installing-and-configuring-openssh-on-windows-server-2019/ba-p/309540), but there are some steps required to get it going on an older server. Let's talk about it.

## The Steps

first, on the server, i ran the following commands in a pwsh 7.2.5 admin console. In plain language, i downloaded the latest (at this time) openssh port for powershell, ran the install, added a firewall rule, set the sshd to auto start, created a new user (real info removed), and set the default shell to powershell. It seemed to default to cmd which caused some of my original script to fail since it expected powershell. after all that, i restarted the service.

```
PS C:\Windows\System32> iwr https://github.com/PowerShell/Win32-OpenSSH/releases
/download/v8.9.1.0p1-Beta/OpenSSH-Win64-v8.9.1.0.msi -OutFile C:\Users\!rashbroo
k\Downloads\OpenSSH-Win32-v8.9.1.0.msi
PS C:\Windows\System32> C:\Users\!rashbrook\Downloads\OpenSSH-Win32-v8.9.1.0.msi

PS C:\Windows\System32> get-service | findstr ssh
Running  ssh-agent          OpenSSH Authentication Agent
Running  sshd               OpenSSH SSH Server
PS C:\Windows\System32> netsh advfirewall firewall add rule name=SSHPort dir=in
action=allow protocol=TCP localport=22
Ok.

PS C:\Windows\System32> Set-Service -Name sshd -StartupType "Automatic"
PS C:\Windows\System32> net user SSHUSERNAME SSHPASSWORD /add
PS C:\Windows\System32> New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name De
faultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -P
ropertyType String -Force

DefaultShell : C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
PSPath       : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\
               OpenSSH
PSParentPath : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE
PSChildName  : OpenSSH
PSDrive      : HKLM
PSProvider   : Microsoft.PowerShell.Core\Registry


PS C:\Windows\System32> get-service sshd | Stop-Service
PS C:\Windows\System32> get-service sshd | Start-Service
```

after all that, on the client, i ran `ssh-keygen` to make the keys. then ran ssh SSHUSERNAME@myserver to connect with a password. this let me know things were working, and i created the folder and file to hold the key with `md .ssh`, `cd .ssh`, and `new-item authorized_keys`.

then i opened up authorized_keys and pasted the contents from id_rsa.pub on the client. we could copy this file using scp as well, but i did it in the ui. because i needed to be on there anyway to fix the permissions.

i didn't check to see if this worked without the permissions fix since there were references to this on all the pages i checked, so next i went to the .ssh/authorized_keys file on the server (via rdp) and went into permissions, turned off inheritance, let it copy the existing permissions, then removed everything except system and my SSHUSERNAME account.

after this, i went to remove the match group admins line in the sshd_config file. `notepad C:\ProgramData\ssh\sshd_config` and then comment out the last two lines. so they'll look like this:

```
# Match Group administrators
#        AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys
```

after this, i added the key on my client machine using `ssh-add id_rsa` and all was right with the world. =)


### Some helpful resources I found that almost got me there:

- https://stackoverflow.com/questions/16212816/setting-up-openssh-for-windows-using-public-key-authentication#:~:text=Some%20guides%20suggest%20running%20the%20Repair-AuthorizedKeyPermission%20%24env%3AUSERPROFILE.sshauthorized_keys%20-,yourself%20should%20have%20full%20control%20over%20the%20file.
- https://www.kjctech.net/setting-up-sftp-or-ssh-server-on-windows-server-2012-r2/#:~:text=Steps%20to%20set%20up%20a%20SFTP%2FSSH%20server%20using,service.%20If%20succeed%2C%20you%20are%20all%20set.%20
- https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement


## The Why

in a previous life, i connected to this server with a mapped drive. i switched to a mac a year or so ago and had some jobs on a 2019 server that i setup deployment using some relatively straightforward microsoft documentation. now i'm moving some jobs to this older server and needed to set it up. i have [a script](https://github.com/royashbrook/PSAgentDeploy) that can be used to deploy jobs, but this didn't work on the older server since no ssh. i was hoping there was a pretty easy way to get ssh on here rather than look for some other retrofit. jobs can be copy deployed, but why not use the script since it's there?

## additional thoughts

the setup for this isn't too bad, but all of the permission things and setting a different default shell, etc, makes things a little weird. i'm guessing there is a cacls line that matches with the chmod lines you can find for setting up on non-windows, but alas. if i have to do this a second time in 10 years, maybe i'll research that and just make this into a script, but for now this hopefully this helps someone else, or future me, out as a reference. i like to _hope_ in the future, no one will need to install ssh on a 10 year old operating system.

