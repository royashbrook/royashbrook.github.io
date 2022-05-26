---
layout: post
published: true
title: Setting up PowerShell on my RasPi
subtitle: What's easier than pie? Prepping the RasPi for PowerShell!
image: 'https://www.raspberrypi.org/app/uploads/2015/08/raspberry-pi-logo.png'
---
I own several raspis that I have fiddled around with for various reasons over the years. Recently, the only one I have had running anything permanently is my [pihole](https://pi-hole.net/). It works great, but generally the raspi is fairly dormant so I wanted to perhaps get some other items running on there. While I could use python for everything like a normal person, I generally use powershell at work for stuff, so I wanted to get powershell running on there. I recently wrote about [PowerShell Universal Dashboard](https://www.poshud.com/) so I thought why not get powershell going and get a few dashboards running.

A little googling for [running powershell on raspi](https://www.google.com/search?q=running+powershell+on+raspi&oq=running+powershell+on+raspi&aqs=chrome..69i57.3630j0j7&sourceid=chrome&ie=UTF-8) got me [here](https://blogs.technet.microsoft.com/stefan_stranger/2017/12/28/running-powershell-core-on-raspberry-pi-2/) which made me thing I should go and check out the [main powershell page](https://github.com/PowerShell/PowerShell) which got me [here](https://github.com/PowerShell/PowerShell/blob/master/docs/installation/linux.md#raspbian) for some official instructions which basically looked the same.

Powershell page says Raspiban Stretch is a pre-requisite and I'm already running it, so these are the commands copied from the install instructions. I went ahead and added the symlink so i could just run pwsh instead of ~/powershell/pwsh. 

```shell
sudo apt-get install libunwind8
wget https://github.com/PowerShell/PowerShell/releases/download/v6.0.2/powershell-6.0.2-linux-arm32.tar.gz
mkdir ~/powershell
tar -xvf ./powershell-6.0.2-linux-arm32.tar.gz -C ~/powershell
sudo ~/powershell/pwsh -c New-Item -ItemType SymbolicLink -Path "/usr/bin/pwsh" -Target "\$PSHOME/pwsh" -Force
```

I'm connected to my raspi from a windows machine via putty. So I just copy/pasted the lines above into putty and after this I was able to run `pwsh` and open up powershell with no issues.

Now I Removed this pihole setup a while back after my wife complained it was blocking too many things. After about a month or so she got fed up with the ads again and requested it's return. Successful user dependency! If my wife needs it, I normally feel that is justification that it is something a normal human would find useful and it's not just a cool tech trick. I mention this because I forgot to setup a scheduled update of all of the packages with apt-get on the 're-setup' of this raspi. Oops! So I went ahead and performed and apt-get update and upgrade to get my system right. =) So don't forget to schedule some sort of automatic upgrade on your raspi.
