---
layout: post
title: P2V Windows 10 and Parallels
---

I have been running Windows 10 since the preview was released. My experience, so far, would be positive. It has been very smooth transition. As someone using a machine without a touchscreen, I would say it is a lot more functional than Windows 8/8.1. I still find the full screen apps lacking, but the operating system is smoother, overall, to me, and I enjoy using it.

I own, and generally enjoy using, a MacBook Pro 15. My only complaint on the MacBook is the lack of physical Home, End, Insert, and Delete keys. I have run Windows of various versions on it in the past using Parallels to great success.

Windows 10 has been happily living on my PC laptop, but I wanted to consolidate it, if I could, onto the MacBook Pro, so I could carry one thing. The resolution on the MacBook Pro is much higher as well and makes some of my work much easier. I am typing this on my Windows 10 VM that was restored onto Parallels on the MacBook Pro, and it's been working for a few days like a champ. I thought I would document the steps I took to move from the Physical machine to this Virtual machine if anyone else needed, as it was not that complicated once I located the information I needed.

1. Make a system image backup of Windows 10
2. Restore the system image backup of Windows 10 onto Parallels VM

That is the high level, now I'm going to go through the steps I took to get there.

1. Make a system image backup of Windows 10

    1. I grabbed a USB drive, and plugged it in where it was recognized as F:
    2. Ran the following command
        - `wbAdmin start backup -backupTarget:F: -include:C: -allCritical -quiet`
    3. Note that you will need to replace F: with whatever drive you want to use.

2. Restore the system image backup of Windows 10 onto Parallels VM
    1. Note: This was, for me a little trickier because my VM didn't seem to see my USB drive however I mounted it to the machine, so I used my NAS.
    2. Connected to a share on my NAS from my PC
    3. Copied the folder with the backup from my USB drive to the share.
    4. On Mac, Open Parallels, selected my WinX ISO
    5. Let Parallels do the entire install (I happened to be going somewhere for a while, so this just completed while I was gone. It's not really required)
    6. Held SHIFT and restart to get into the recovery console
    7. Went to repair, advanced, system image recovery (path may be a little different, but as long as you get to system image recovery, it's ok)
    8. Stepped through wizard and mapped drive to NAS share
    9. Picked backup and restored
    10. Restarts happened after that as needed.
    11. Once Windows was started, took a snapshot and began cleaning
    12. Installed Parallels tools and removed the driver software for my hardware I had installed
    13. Took another snapshot when all was stable and start working.

One thing to note is that the Parallels tools need quite a few reboots. So if you are rebooting over and over again manually, you may think something is wrong, but it is probably fine. I wasn't being super observant, but I think it was 4 or 5 reboots that all looked like they did the same thing when they started.

This was my first time using the system image recovery tool, and I have to say compared to Time Machine, it's a bit ridiculous and convoluted. It *did* work though once I found the commands and figured out what to do. By contrast, when I got my new mac, I just pointed it at my Time Machine location (which setup found automatically for me) and pushed a button. I imagine a OSX VM would work the same way. Apple! Why don't you have HOMEENDINSERTDELETE KEYS!!! Then you would be perfect!

=P

For the page that I drew my inspiration from, look here:

http://www.howtogeek.com/167984/how-to-create-and-restore-system-image-backups-on-windows-8.1/