---
layout: post
title: Rename a Connection and change DNS servers
---

Recently I was fiddling with a virtual lab in azure. I eventually shut down all of the servers except one application server I was still working with a little bit. I had setup the server to originally use a domain controller I setup for DNS, AD, etc. I shut that down though and didn't want to turn it on just to cache requests. I really didn't want to change anything about anything, but every time I rebooted the application server I would get DHCP DNS and it wouldn't work. I don't typically need DNS on this test server and would only remember that DNS wouldn't work if I needed to browse the web or something. Long story short, I just wrote this little script to rename the connection if needed and change the DNS servers to use google's public DNS servers. I only have one NIC so this works fine. If you had more than one, you'd probably want to call out.

```sh
for /f "tokens=4 skip=3" %%i in ('netsh interface show interface') do set _on=%%i
netsh interface set interface name="%_on%" newname="vNIC"
netsh interface ipv4 add dnsserver "vNIC" address=8.8.8.8 index=1
netsh interface ipv4 add dnsserver "vNIC" address=8.8.4.4 index=2
```

I should note that netsh is supposed to go away at some point. You can also do this in powershell.