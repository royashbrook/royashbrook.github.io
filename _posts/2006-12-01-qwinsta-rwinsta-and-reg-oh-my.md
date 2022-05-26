---
layout: post
title: qwinsta, rwinsta, and reg, OH MY!!
---

i figured i would make a quick post of some rdp type info i know. realize that all of these require you have the appropriate permissions.

have you ever tried to login to a machine via rdp only to get the message that there are too many sessions? well here's how to kill one of those sessions. first of all, you need to see what sessions there are. to do that, we can utilize the command line tool qwinsta. type qwinsta /? to see the syntax. here's a sample:

```
C:\>qwinsta /server:server1
 SESSIONNAME       USERNAME                 ID  STATE   TYPE        DEVICE
 console                                     0  Conn    wdcon
 rdp-tcp                                 65536  Listen  rdpwd
 rdp-tcp#10        user1234                  2  Active  rdpwd
```

as you can see, user1234 has an active connection on id2. now, typically, someone has an inactive session or something like that. and that's who you'd want to kick. but let's pretend we know that user1234 is just doing something silly like surfing the web on server1 and kick him off. to do *that* we'll utilize rwinsta to reset the connection. at this point you can see the complex naming system emerge. rwinsta for resetting connections, and qwinsta for querying connections. as per usual, type rwinsta /? to see syntax. here's an example:

```
C:\>rwinsta 2 /server:server1
```

if you requery, you will see that the session is gone. the other person will get a message saying the session was ended, that it may have been ended by an administrator etc. now you can connect via rdp.


BUT! .... what happens if you are trying to connect via rdp and it isn't enabled. well, you'll probably get an access denied message or some other error when you try and perform a qwinsta, but if rdp isn't turned on, you would already know it's not working because you wouldn't even get a logon screen when you tried to connect via rdp. you'd get an error message instead.

here are some simple commands to check and set rdp on a server:

```
check:
reg query "\\server1\HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections

set:
reg add "\\server1\HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD 0

the check will return something like this:

HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server
    fDenyTSConnections  REG_DWORD       0x1
```

0x1 means the DENY is true. so you can't connect. you want to set it to 0 as in the set example above to DISABLE the DENY. why they couldn't just make it an enable setting and then we could just set it to true or false. yay double negatives in the windows registry. =P

after you set this, you'll have to reboot the machine, which you can simply use the shutdown tool for.

```
shutdown -r -f -m \\server1
```

and just in case you say "OH CRAP, I DIDN'T MEAN TO SHUT THAT DOWN" right after you run the command above, here's the abort command too.

```
shutdown -a -m \\server1
```

that includes our fun lesson for the day. hopefully that helps someone. i know that qwinsta and rwinsta have been two of the most often used tools in my administrative life/half-lives =P
