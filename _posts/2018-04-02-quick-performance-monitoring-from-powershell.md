---
layout: post
published: true
title: Quick Performance Monitoring from Powershell
image: 'https://i.imgur.com/yNlQWRM.jpg'
subtitle: but why tho
---
If, you wanted to take a quick peak, using powershell, on some performance metric and show it in write-progress, here's how you would do it.

"But why?" I hear you saying. "Can't I just do that in perfmon or any other tool made for this?"

Well, sure, but where's the fun in that. =)

{% gist ddefc527ba3f403473360beefe5288f0 %}

If you want to see them both, should be simple enough to setup multi-threading and get some feedback. [Here's](http://www.get-blog.com/?p=22) an example of someone doing something similar.

_2019-02-12 Edit:
For some reason I decided to look at this again. I wanted to be able to see all of my processors at once. So here is something for that:_

```powershell
function ShowProgressBars{
    Begin {} Process {
        $i = 0
        foreach($sample in $_.samples){
            $a = "$($sample.path)"
            $p = ([math]::round($sample.cookedvalue))
            Write-Progress -Id (++$i) -Activity $a -PercentComplete $p
        }
    } End {}
}

((Get-Counter -ListSet Processor).PathsWithInstances | ? {$_.contains("% Processor Time")}) |
    get-counter -SampleInterval 1 -Continuous |
    select @{n="samples";e={$_[0].CounterSamples}} |
    ShowProgressBars
```
