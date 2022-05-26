---
layout: post
published: true
title: Using powershell to compress data in flight
subtitle: 'Why don''t we buy a bigger internet circuit, again?'
date: '2018-11-27'
image: /img/bunsquish_1024x1024.jpg
---
I have a situation where I need to copy some data over a WAN link. This is a MS SQL backup that needs to be restored at a remote location when we relocate a system. We generally can get ~8-10MB/s while copying the files and the database backup is 300+GB so this takes a long time. Bandwidth is our choke point, so in an attempt to reduce the amount of data sent over the wire, I wanted to stream the files and compress them in-flight.

Due to time constraints, I did not do a lot of in depth testing, but I was able to see a large improvement. There are probably plenty of other ways to do this (on \*nix, this is something we could do with tar/scp natively as far as I can tell) as the backup files were split into multiple ~40GB files and compressed those would show up on the other end as ~7GB files. 

As my testing was fairly on the fly and not very "scientific", I just wanted to document the methodology used. Below is the code with a function for streaming from one location to another with a variable buffer size. Below that are commands to execute. Could easily have been a 1..8 loop with start commands built in, but was just as easy to copy/paste for my purposes for a one off. Also ran these in individual powershell windows (so had to copy in the function for each one) which pushed my throughput from ~20MB/s up to ~60MB/s, coupled with the smaller file this made things a lot faster. As with all things, with tons of tweaking, I'm sure this could be optimized or another approach could be done, but this was a one off task, so I just spent some time on this off and on and once the copy time came down to something sort of reasonable, I left it there.

These machines are on different domains with no trust, so drive had to be manually mapped.

``` ps1
function a($in,$out,$bs){
    [byte[]] $b = new-object System.Byte[] $bs
    $m = [IO.Compression.CompressionMode]::Compress

    $u = [IO.File]::OpenRead($in)
    $c = [IO.File]::Create($out)
    $g = new-object IO.Compression.GzipStream $c,$m
    while (($cb = $u.Read($b, 0, $b.Length)) -ne 0){ $g.Write($b, 0, $cb) }
    $g.Dispose()
    $c.Dispose()
    $u.Dispose()
}

measure-command { a G:\20181120-Test\db1.bak Y:\20181120-Test\db1.bak.gz 8192 }
measure-command { a G:\20181120-Test\db2.bak Y:\20181120-Test\db2.bak.gz 8192 }
measure-command { a G:\20181120-Test\db3.bak Y:\20181120-Test\db3.bak.gz 8192 }
measure-command { a G:\20181120-Test\db4.bak Y:\20181120-Test\db4.bak.gz 8192 }
measure-command { a G:\20181120-Test\db5.bak Y:\20181120-Test\db5.bak.gz 8192 }
measure-command { a G:\20181120-Test\db6.bak Y:\20181120-Test\db6.bak.gz 8192 }
measure-command { a G:\20181120-Test\db7.bak Y:\20181120-Test\db7.bak.gz 8192 }
measure-command { a G:\20181120-Test\db8.bak Y:\20181120-Test\db8.bak.gz 8192 }
```

Here's a picture of a fancy zipper. =)

![Fanzy Zipper]({{site.baseurl}}/img/Article_Innovations_Zip-necklace_U-B2.jpg)
