---
layout: post
published: true
title: Watching a folder for changes and sending an email with powershell
subtitle: Who watches the watcher?
date: '2019-02-05'
image: >-
  https://vignette.wikia.nocookie.net/marveldatabase/images/3/3a/Uatu_%28Earth-616%29_from_Original_Sin_Vol_1_1_001.jpg/revision/latest/scale-to-width-down/267?cb=20140507202821
---
I [previously wrote]({{site.baseurl}}/2018-07-24-watching-files-with-powershell/) about watchintg a folder for changes and displaying some information on those changes. This was primarily used for some live monitoring of a file mover that was being sunset.

After some use, like all good techy people, I wondered why I should even display this to the screen. I mean I did want to log the changes, but really I just wanted to be notified that the event (the file moving) had started, and then I could review the logs. So, to that end, I tweaked my process a bit. I would monitor the files in one powershell window with a similar method to the previous one, showing all of the create/deletes, but I would add an extra window that would just shoot me a text (via email) when the event had started.

To do this, just need to have a separate console session and have the triggered action send an email and then *stop monitoring* otherwise you may get a million texts like I did the first time I tested this.

Script I currently use for monitoring a folder and showing the results in a console window:

{% gist bf8419a82dbc6de5c4a7b160256d446b watchfolder.ps1 %}

Script I currently use to just notify myself via email to text when a file was created, aka, the event started:

{% gist bf8419a82dbc6de5c4a7b160256d446b notify.ps1 %}

Note that these aren't 'industrial strength' modules or anything, just fairly utility scripts that could almost be one liners

Bonus: another item I sometimes need to do is just see the traffic that is coming in on a particular site or for a particular user or something. I generally just do something like the below for this:

```powershell
# get the most recent log file from server
$f = (gci 'unc path to iis logs' | sort lastwritetime -desc |select -first 1)

# a simple color coding script for the output, setup to use the pipe for input
function cc{
	begin{}process{
	$c='red'
	if($_.contains(' 200 ') -or $_.contains(' 302 ') -or $_.contains(' 401 ')){$c = 'green'}
		$_ | write-host -f $c
	}end{}
}

# grab the last 50 lines, then keep reading in the values, and color code the output
$f | gc -tail 50 -wait | ? {!$_.contains('/somevirtualdirtoexclude')} | cc
```

I add and remove things in the cc function as needed as well as modifying the where. This is just consuming iis logs, but i have tweaked it for other live logs that allow you to access them with tail.
