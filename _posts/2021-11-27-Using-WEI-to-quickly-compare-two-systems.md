---
layout: post
published: true
title: Using WEI to quickly compare two systems
subtitle: >-
  wait... wei still exists?
date: '2021-11-27'
---

# TLDR;

On both machines, in powershell:

```ps1
winsat formal
[xml]$results = Get-Content "$env:systemroot\Performance\WinSAT\DataStore\*formal*recent*.xml"
$results.WinSat.WinSPR
```

# Why I wrote this

I'm sure I am not the only one that serves as the CTO/CIO/CISO and any other C* in IT for their family. This results in a wide array of requests, most of them are pretty simple like this one.

Recently, a family member had a need to revive an old laptop as their personal device. *That* led to a request for me to re-setup the machine. The machine in question was a beast of a desktop replacement laptop from 2013. Let's call that Machine A. As I was working on it, I told them that I actually had a laptop I just left on my desktop for scanning family machines and similar situations that may be the same. Let's call that Machine B. Machine B was quite a bit more portable than machine A since it was a 15" laptop. Machine A was a 17" beast. I'm not sure about everyone else, but when I am doing a task like this, I generally compare the machine in question to my current graveyhard of existing machines, some of which I am still using, and just see if my graveyard machines would be better than the one being revived.

But how to compare the two systems? Machine A was from 2013 and was top of the line and machine B was from 2012 and was, if I recall, pretty top of the line at the time as well, although not on the level with this beast. Let's write a quick glance comparison.

| Measure    | Machine A   | Machine B   |
|------------|-------------|-------------|
| Age?       | 2013        | 2012        |
| Processor? | i7          | i7          |
| Memory     | 8GB         | 8GB         |
| Company    | Good        | Good        |
| Storage    | 512GB (HDD) | 512GB (SSD) |

Without getting into the numbers on the i7, the biggest difference looks like the SSD which I would, out of the gate, assume to make a huge difference. But since Machine A is the existing machine to re-setup, and Machine B is the one I have sitting here, I don't want to offer a 'worse' machine. So... how to do this quickly. Well, that's the point of writing this down...

## WEI!

Windows Experience Index, or WEI, used to come on windows machines, but no longer does. You can read more about it [here](https://en.wikipedia.org/wiki/Windows_System_Assessment_Tool). While Microsoft removed it from easy view, you can still get a score using the command line.

There are a variety of ways and options for running the tool, but I really just want to get the overall score and the detail scores for a quick comparison. 

To do this we need to open a command line interface, cmd or powershell will work and run `winsat formal`. This will open another window that will run the performance tool and then close it.

You can list the output files like below:

![image](https://user-images.githubusercontent.com/7390156/143688042-7ed9f4bc-3719-4d62-8a4b-c2f1b98f149d.png)

I have run this tool myself before, so you can see there are files with `(Initial)` from some time ago and some with `(Recent)` from a moment ago. For this purpose, probably either of those will work, but I will use Recent since I just ran it.

The output files are xml, so we can read the properties we want with powershell like this:

```ps1

#To get the file name we want, you can type in wildcards, and then hit tab so you type in this:
#"$env:systemroot\Performance\WinSAT\DataStore\*formal*recent*.xml"
#hit the 'tab' key and it will expand it to something like this:
#"C:\WINDOWS\Performance\WinSAT\DataStore\2021-11-27 10.46.12.599 Formal.Assessment (Recent).WinSAT.xml"
#In our case, we want to read the file in as xml, and the easiest way is just to cast it to xml while reading it, so we type this:
#[xml]$results = Get-Content "$env:systemroot\Performance\WinSAT\DataStore\*formal*recent*.xml"
#hit the 'tab' key and it expands to something like this:
#[xml]$results = Get-Content "C:\WINDOWS\Performance\WinSAT\DataStore\2021-11-27 10.46.12.599 Formal.Assessment (Recent).WinSAT.xml"
#you _can_ actually simply run this command if you want:
#[xml]$results = Get-Content "$env:systemroot\Performance\WinSAT\DataStore\*formal*recent*.xml"#
#but I prefer to hit tab so I know what I'm actually opening normally. I'll do the shortcut below though for simplicity.

[xml]$results = Get-Content "$env:systemroot\Performance\WinSAT\DataStore\*formal*recent*.xml"
$results.WinSat.WinSPR

```

This will give you some kind of score like this:

![image](https://user-images.githubusercontent.com/7390156/143688535-e6cf6ec0-138d-4dbb-8ab3-b741944982f5.png)

You'll notice there are lots of 9.9 which is where I think it caps out, but it does give you a few other spots to compare.

How did my two systems compare?

| Measure          | Machine A | Machine B |
|------------------|-----------|-----------|
| SystemScore      | 5.9       | 6.4       |
| MemoryScore      | 8.9       | 8.6       |
| CpuScore         | 8.9       | 8.6       |
| CPUSubAggScore   | 8.0       | 7.3       |
| VideoEncodeScore | 9.9       | 9.9       |
| GraphicsScore    | 8.2       | 6.4       |
| Dx9SubScore      | 9.9       | 9.9       |
| Dx10SubScore     | 9.9       | 9.9       |
| GamingScore      | 9.9       | 9.9       |
| DiskScore        | 5.9       | 8.4       |

Graveyard PC scores higher on the overall systemscore, but if we look at the breakdown scores we can see Beast machine *really* wins everywhere except disk. So my recommendation was pretty simple, Machine A _should_ probably get a new SSD and it'll do the job for a long time. But it works fine as is. I think, in this case, the major decider might be the portability since Machine B is far more portable than Machine A. Regardless of the choice, this is a quick way to compare two systems.

I will note that both machines were running Win10 fully patched, probably best to always ensure all of the drivers are similar so these comparisons are apples to apples as best as possible. Astute readers may have noticed the scores in my screenshot are different, that is because I wrote this blog post on yet another machine that is also no rockstart as well. It works fine, but looking at this I can see it's not even as good as the old machine from 2013 lol. I'll console myself with the fact that this computer has 16GB of RAM I suppose. =P

Any questions and you can hit me up on twitter [@RoyAshbrook](https://twitter.com/RoyAshbrook)

I hope this helps out someone else out there =)
