---
layout: post
published: true
title: Simple FTP File Listing with PowerShell
---
Had a need to do this recently. I just needed to get a file list from one directory. You can actually just use a networkcredential here, but I use the credential myself later and I need a pscredential object.

``` powershell
# credentials
$uri = New-Object System.Uri("ftp://ftp.whatever.com/path/")
$u = "username"
$p = "password"
$c = New-Object pscredential ($u,(ConvertTo-SecureString $p -AsPlainText -Force))

# get listing of new files
$req = [System.Net.FtpWebRequest]::Create($uri)
$req.Credentials = $c.GetNetworkCredential()
$req.Method = [System.Net.WebRequestMethods+FTP]::ListDirectory
$rs = $req.GetResponse().GetResponseStream()
$sr = New-Object System.IO.StreamReader $rs
$files = do {$sr.ReadLine()} until ($sr.EndOfStream)
$sr.Dispose(); $rs.Dispose()
 ```
