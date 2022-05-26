---
layout: post
published: true
title: Queue up or pre-download windows updates with powershell
image: 'https://cdn.drawception.com/drawings/VHhbdnH51a.png'
subtitle: How did we get here?
---
I hope no one reading this ever actually has to do this. In my case, I have some old'ish servers that have very limited downtime windows. While I was updating these I noticed that a lot of them still did not actually 'download' the updates even though they were set to download and install later.

Every time I would get on one to do updates it would first have to run a lot of checks and take a lot of time to go check vs just running the updates it had first. We can check after the first reboot! OK, computer?

So to fix this, I used the following powershell to download the updates.

```powershell
    $o = new-object -c microsoft.update.session
    $s = $o.CreateupdateSearcher()
    $u = @($s.Search("IsHidden=0 and IsInstalled=0").Updates)
    $items = $u | where IsDownloaded -eq $false
    $d = $o.CreateUpdateDownloader() 
    $d.Updates = new-object -c microsoft.update.updatecoll
    $results = foreach($item in $items){
        [void]$d.Updates.add($item)
        $r = $d.Download()
        [void]$d.Updates.RemoveAt(0)
        [pscustomobject] @{
            item = $item
            result = $r
        }
    }
    #view results
    $results | %{ [pscustomobject] @{title = $_.item.Title;resultcode= $_.result.resultcode} }
 ```
 
 [Source](https://msdn.microsoft.com/en-us/library/windows/desktop/aa387291(v=vs.85).aspx) for some of the results you'll get back. You can also just add all of the downloads to the updatecoll and submit all of them at once, but i wanted to do these one at a time just to be safe.
 
 Here is also a way to install these upgrades if you want to and aggregate the results. You can also consult history if you like. Here I am using invoke-command (icm) with a script block
 ```powershell
 $sb = {
    $o = new-object -c microsoft.update.session
    $s = $o.CreateupdateSearcher()
    $u = @($s.Search("IsHidden=0 and IsInstalled=0").Updates)
    $items = $u | where IsDownloaded -eq $true
    $d = $o.CreateUpdateInstaller() 
    $d.Updates = new-object -c microsoft.update.updatecoll
    $results = foreach($item in $items){
        [void]$d.Updates.add($item)
        $r = $d.install()
        [void]$d.Updates.RemoveAt(0)
        [pscustomobject] @{
            item = $item
            result = $r
        }
    }
    ,$results
}
$results = icm -sc $sb
(Get-History)[-1]|fl *
$results | %{ [pscustomobject] @{title = $_.item.Title;resultcode= $_.result.resultcode} }
 ```

Generally, I think I would always prefer to let windows update do it's own thing, but this was a sort of a special circumstance because we could not hit the 'install' button (which would download and install things) but we could download items before the 'change window' came into play.

Also, if you want to see the status of the updates (although it's not terribly easy to read) you can check out the windowsupdate.log file like this `gc \\server\c$\windows\windowsupdate.log -tail 10 -wait` or some other similar `tail` type command. The log is in %windir%, but I was looking at a remote machine so I put in the path.

[source](https://support.microsoft.com/en-us/help/902093/how-to-read-the-windowsupdate-log-file)
