---
layout: post
published: true
title: Move inactive users to a new Exchange 2010 Database
subtitle: How can I create a new database and move "inactive" users to it
image: 'https://sd.keepcalm-o-matic.co.uk/i/user-inactive-please-carry-on.png'
---
## Archiving inactive users to another database

![child on couch reading an magazine titled inactivity](https://s3.amazonaws.com/lowres.cartoonstock.com/children-children-childhood-fat-lazy-computer-cgon637_low.jpg)

I am not an 'Exchange Guy' but we are working to migrate to O365 and so just limping along our existing infrastructure. We do need to make some space in some existing databases, so we are wanting to shuffle some of the 'inactive' users to another database on a different drive. So here are the steps to create the database and move a bunch of the users. _note: I am using Exchange 2010 SP3_

``` powershell
#server name
$s = "your servername"

#https://technet.microsoft.com/en-us/library/dd297932(v=exchg.141).aspx
# create powershell session to your server
$Session = New-PSSession -ConfigurationName Microsoft.Exchange `
	-ConnectionUri "http://$s/PowerShell/" `
	-Authentication Kerberos
Import-PSSession $Session
#Remove-PSSession $Session # when finished

#create a new database for our inactive users
# note i am sticking the log in the same location as this more of an archive and i have circular logging enabled. this isn't a best practice, but i only had one extra drive. =)
$db = 'Inactive Users'
$p = 'path to folder you want this db in'
new-mailboxdatabase -server $s -name $db -EdbFilePath '$p\db.edb' -LogFolderPath $p
Set-MailboxDatabase $db -CircularLoggingEnabled $True
Mount-Database $db

#get mailbox stats
$mbs = Get-MailboxStatistics -Server $s

#get inactive users, i am just getting some fields i want, formatting them, and looking for folks who have logged in sometime in the last three months.
$iu = $mbs |
     select *,@{n="sz_byte";e={tb($_.totalitemsize)}} |
     where lastlogontime -lt (get-date).addmonths(-3) |
     sort sz_byte -Descending |
     select DisplayName, ItemCount, LastLogonTime, TotalItemSize, @{n="sz_GB";e={[math]::round($_.sz_byte/1024/1024/1024,2)}}

#look at them (if you want to)
$iu | ft *

# here i am going to get disabled ad users and we'll just use those as inactive as well
$du = Get-ADUser -filter * | where enabled -eq $false | ? { $_.givenname.length -gt 0}
$du_mbs = foreach($u in $du){Get-MailboxStatistics $u.samaccountname}

#below here, $du_mbs and $iu are fairly interchangable

# some of these folks may have large mailboxes. as they are inactive, we'll just remove all the quotas so we can archive them
$du_mbs | %{ Set-Mailbox $_.displayname -UseDatabaseQuotaDefaults $false -IssueWarningQuota unlimited -ProhibitSendQuota unlimited -ProhibitSendReceiveQuota unlimited}

#create a quick function to move these folks
# spit out the email and then start the move request
function mvmb($name){
	($email = (Get-Mailbox $name | select windowsemailaddress).windowsemailaddress)
	New-MoveRequest -Identity $email -TargetDatabase $db > $null
}

#move a subset of the users
# you can move them all instead, but in my case i was sending batches up of various sizes
$iu[5..25] | %{ mvmb $_.displayname}

#you could also do this in line like this, get all of their email addresses
$ea = $du_mbs | ?{$_.database -ne $db} | %{ (Get-Mailbox $_.displayname | select windowsemailaddress).windowsemailaddress}

#now, move all of them
foreach($e in $ea) { New-MoveRequest -Identity $e -TargetDatabase $db }

# check status on moves
Get-MoveRequest | Get-MoveRequestStatistics

# I also had a couple of moments where i wanted to see the size of things moved and filter through the other details, for that I used something like this:
Get-MoveRequest | Get-MoveRequestStatistics | where status -eq 'completed' | select *,@{n="sz_byte";e={tb($_.totalmailboxsize)}} | ft @{n="sz_GB";e={[math]::round($_.sz_byte/1024/1024/1024,2)}},*
```

As with all things powershell, there are a ton of ways to do things. I wouldn't say everything here is the most efficient and consistent, but I think it provides a basic idea if you need to move some accounts.

_Future Me: if you are still on Exchange SP2010, sorry about that! You really should look into O365, it's swell!_
