---
layout: post
published: true
title: How to relate VMWARE disks to Windows disks
---
In recent months I have had quite a few ocassions to reconfigure some virtual machines that have have hosted on a VMware environment. The most common situation is something like this:

- We need to move database/file/app/etc to machineX.
- Sys admin attempts to copy, oh no machineX doesn't have enough space on driveX!
- The VM has 70% free space and shows that there is enough free space
- Nothing is on half of the drives, so we need to remove those extra disks and reallocate the free space to driveX or driveNew.

Nothing too crazy, but if you have a bunch of disks on a machine, how do you ensure you are removing the correct one in vSphere Client? Well, the answer is probably, holistically, "it depends on your environment" because maybe things are configured differently. But for me, it came down to matching up the SCSI IDs. You can get these disk IDs by going into disk manager, right clicking on a drive, and looking at the port/id combo there. And this is what I actually did for some time. But I figured I could get all of them out in some sort of powershell script and after a little research, this is what I came up with.

```powershell
Get-WmiObject -Comp "your machine name" Win32_DiskDrive |
	select index, SCSI*, `
    @{n='Size_GB';e={[math]::round($_.size/1024/1024/1024,2)}} |
    ft *
```

I have actually been using the Win32_LogicalDisk call for inventorying drives for ages. I googled win32 physical disk or something similar and found this call in short order on the MS site. Then just did a dump of the first one to figure out the properties I wanted and all done.

Most of my machines are on a single logical SCSI controller in the machine config, but if you added multiples or multiple busses or something, I would imagine those would show up here. I did have one machine like that in the past and I had to kinda guess based on the size which bus was correct.