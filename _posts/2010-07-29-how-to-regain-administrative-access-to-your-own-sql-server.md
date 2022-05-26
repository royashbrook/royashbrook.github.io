---
layout: post
title: How to regain administrative access to your own sql server
---

Question: how do I regain sysadmin role for myself if I am a local admin, but not a sql sysadmin currently. This scenario can happen for a variety of reasons, but if you are the owner and administrator of a box, how do you get to where you can manage it again? It's pretty simple. Basically stop sql, then start it in single user mode, then add yourself, reset sa, whatever. A sample script is below. This script makes several assumptions.

1. Your server doesn't take longer than 10 seconds to start, otherwise modify the sleep line (or just do this manually and wait for it to start)
2. You already have an account with access on the server. If you don't you can add it or just reset the SA password. I find that usually the people interested in doing this at least have access to a database on the server so you can just grant that group admin access for the duration of the recovery.
3. You don't mind killing the sql server task ungracefully like I'm doing below. If you do mind that, you can always just do all this one step at a time instead of copy and pasting it or something.
4. You're server binary is in Q:\MSSQL\MSSQL.9999\MSSQL\Binn\sqlservr. I assume that yours is not which is why I am using this in the script so change the path to wherever yoru sqlservr.exe is actually located.
5. You already had sql agent running. If you didn't, you can drop the last line. If you did the first line will automatically end it with the /y
 
```plaintext
net stop mssqlserver /y

start "sqlserver" Q:\MSSQL\MSSQL.9999\MSSQL\Binn\sqlservr -x -m

sleep 10

sqlcmd -E -S localhost -d master -Q "EXEC master..sp_addsrvrolemember @loginame = N'domainname\somedomaingroupthatalreadyhasanacccount', @rolename = N'sysadmin'"

taskkill /IM sqlservr.exe

net start mssqlserver

net start "SQL Server Agent (MSSQLSERVER)"
```