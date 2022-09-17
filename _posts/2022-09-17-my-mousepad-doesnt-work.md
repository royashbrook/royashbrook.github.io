---
layout: post
published: false
title: my mousepad doesn't work!!
date: '2022-09-17'
---

this year i volunteered to help take care of all of the it needs at my children's school. what posessed me to do such a thing, you ask? well, i just wanted to help out is the simple answer. it has been a lot of work, but pretty fun overall and i hope we can eventually get the kids involved at the school and possibly start some kind of learning program around it operations in some fashion. but that's not what this blog post is about.

## My mousepad doesn't work!!

upon first getting engaged, i did some inventory. there are currently no it folks at the school. one of the things i noticed is we had quite a lot of chromebooks, quite a lot of older macs, and some pcs. the pcs seemed to be purchased by the previous it person who preferred pcs over chromebooks or macs. the teachers all loved macs. this spans a couple of years and some purchases took place during the pandemic, so it's not exactly normal times and i won't digress much on that.

as it stood today, we had about 35 chromebooks, about 35 pcs that looked *exactly* like the chromebooks, and then some pcs and old macs. we also had about 30 *old* chromebooks that were no longer getting updates that were various flavors. it also happened to be that this summar [chromeOS](https://chromeenterprise.google/os/) was finally released. google bought another company that helped make this happen and you can read more about that [here](https://arstechnica.com/gadgets/2020/12/google-acquired-neverware-makers-of-cloudready-chromeos-variant/)

so, the teachers hated the pcs, i don't think they loved the chromebooks, but at the start of the school year there was not budget to buy all new macs or anything. so as an interim solution, and to make things more managable with google (as it was already in house) we put chromeos on the chromebook pc wannabes. these were running windows10 at the time.

so, to explain this section title, when chromeOS was installed, some of the touchpads didn't work. this resulted in many tickets like "my mousepad doesn't work" or "student says computer is broken" etc.

## didn't you test

yes. yes i did. unfortunately i didn't test every computer and these are touch screen so since i was working on sometimes 5-10 at a time, i was normally using the touch screen. plus when i got involved it was a bit of a time crunch to just get things somewhat ready for the new year so it was what it was. i thought a couple of them may have this issue, but it seemed like the minority.

the computers in question were both Lenovo 300e devices. One was a chromebook, and the other was a windows device. so this seemed like such an easy way to standardize the fleet on a chrome experience. at least for these devices. i thought i was set. even if the last person bought devices that didn't seem to be much loved, at least we could have something consistent and things would be happy'er than having chromeos and windows. or so i thought.

## trouble in paradise

there were actually several issues when i started this process. first, it seemed that chromeos (at least the version at the time) would not support the 64gb emmc driver installed already. i already knew from having one of these devices opened there was another hard driver slot, so i ordered some nvme drivers to use instead. these were about 10 bucks each and that seemed like a pretty easy trade off. i got things in, and loaded up chromeos with no issues. once installed, i discovered the second issue, the touchpad not always working. i did notice the touchpad issue, but when i discussed with administration during the summer it sounded like it was no big deal as these are all touch screen tablet machines anyway. i also didn't feel like i saw it on more than a couple of devices, and we were in a rush so we just moved on. finally, while i was waiting for the hard drives to come in, i did go ahead and upgrade and patch these to win11 and get everything setup on them. this was great, but the third issue i ran into was how to manage logins on these devices. i'll skip that for now as we went with chromeos, but i was a little sad to basically turn off everything i setup for dealing with windows. =)

## these touchpads don't work?!

i was tied up and unable to help deal with the ongoing chaos of school starting due to my actual day job when school started. by the time i was able to swing by the school again to discuss, first week of school was over. this is when i found out the biggest issue seemed to be that the touchpads didn't work. so even though this was discussed, it seemed like it was as if those discussions had never happened and lots of folks were sad that the touchpads didn't work. oops. =) sorry folks, decisions were made! 

so since this seemed to be such a major issue, i started trying to figure out why. as with most computer things, it's going to be hardware or software. the touchpads worked on all of these devices in windows, so this seems to be a software thing. i mean they worked on *all* the windows devices, not just some. since all of the 'working' devices were in use, i only had some that didn't work. in hindsight i wish i would have just gotten back one that worked. this is what eventually happened and when i compared the two devices point by point, i found that the newer bios version for the lenovo device didn't work. The one that wasn't working that I had was running version [FRCN21WW](https://download.lenovo.com/consumer/mobiles/FRCN21WW.txt) which was current and released in July 2021 and the working one was running [FRCN18WW](https://download.lenovo.com/consumer/mobiles/frcn18ww.txt) which was released in Feb 2021. After a downlevel and reboot, touchpad was working. as i write this, i have yet to update the other 10ish that i know of that don't work, but fingers crossed.

i also ran into an issue where the bios updater didn't seem to work in windows 11. it would reboot, but do nothing. i had to shift+restart to the recovery menu, then i could run command line and run bios from there and it all worked like a champ. it seemed to wipeout the chromeos bootloader, but i just reloaded chrome and all was well.

## so... happily ever after?

well, not quite. for this issue, yes. but i also have some *other* lenovo devices with other issues with chromeos, including some others with touchpad issues. hopefully an old bios update will work for those as well. i think my biggest current issue is the lpd printing on these chromeos devices doesn't seem to work right with our larger ricoh network printers. that's a post for another day, though. =)