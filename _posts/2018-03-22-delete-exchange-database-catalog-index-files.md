---
layout: post
published: true
title: delete exchange database catalog index files
subtitle: >-
  Oh no, my exchange server database drive filled up! Can i delete all of these
  old .CI files?
image: 'https://pbs.twimg.com/profile_images/547302837986660354/I42YyZn1_400x400.png'
---
## Hey, is the mail server having some weird issues?

I dunno, let me check... hmm... yes, it seems so, let me look at the server... and I'll just open up explorer and...

oh no, we are out of disk space!

![img](http://images1.fanpop.com/images/image_uploads/gasp-princesses-880314_500_333.jpg)

That's right princess! Now what do we do?

Well, after looking at our files, it seems the databases were fine. Meaning, they were not large enough to fill up the drives themselves. But there was a `catalog-[a bunch of letters and numbers` folder that was taking up about half as much as the database itself with many files that hadn't been modified in years.

## Oh no, my exchange server database drive filled up! Can i delete all of these old .CI files?

I am not an exchange expert, but a little googling around and a few emails to some other folks seemed to confirm that these were index files used by exchange search and could be safely deleted and exchange would rebuild whatever it needed. 

I should first say, let me get out of my time machine where I travel from the past since I am using MS Exchange 2010 SP3. =) So keep that in mind if you are reading this and using something newer. We are in the midst of migrating to office 365, so I just need to limp what we have along for another 3-6 months.

__note: Future Me, if you are reading this and you still haven't migrated, don't lose hope, you'll get to the cloud someday!!!__

So for me the commands were like:
``` powershell
# this is the service we need to stop and later start
net stop/start msexchangesearch

# here is what I used to check drivespace
(gwmi win32_logicaldisk -comp 'myserver' -filter 'DriveType = "3"').foreach({
    new-object psobject -prop @{
        drive = $_.deviceid
        size_GB = [math]::round($_.size/[math]::pow(1024,3))
        "free%" = [math]::round($_.freespace*100/$_.size,2)
    }
}) | ft *

# here is how i looked at my databases and got their file info
Get-MailboxDatabase -Status | ft name,Availablenewmailboxspace,Databasesize, edb*

# here is how i turned off/on indexing
Set-MailboxDatabase "name from script results above" -IndexEnabled $false/$true
```

I don't think you *must* disable indexing, but i wanted to make sure I got a clean index built as this may not have been handled in many years. So my steps were basically:
1. Check Disk Space
2. Stop service
3. Disable Indexing on database
4. Move catalog folder to another location
5. start service
6. enable indexing on database

That's it. I got some much smaller indexes and everything was working ok. Worth mentioning this was a VMware hosted machine, so I took a snapshot and did this after backups ran just in case. I think you could just leave the indexing on, but it just seemed safer to make sure the indexing started fresh.

It's also possible I could have simply disabled indexing and then removed the files manually, but what I did is what's up above. =)


