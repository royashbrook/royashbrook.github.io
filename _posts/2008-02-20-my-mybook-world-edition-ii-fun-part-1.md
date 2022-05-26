---
layout: post
title: My MyBook World Edition II Fun =) - Part 1
---

So in my previous post, I set the following goals for my MyBook WE2:

1. Serve as a mirrored storage point for all files I wanted to manually backup.
2. Automatically download torrents from a directory I would put torrents into
3. Automatically download predefined lists of videos and convert them to various formats
4. Consume schedule video from my PCTV2GO and convert it to various formats
5. Download predefined lists of blogs etc I wanted to read and convert them to audio.
6. Serve as a print server? Maybe print out predefined documents for consumption.

Let's see what we can get accomplished. We'll start with the first goal and get our storage point setup. I want to have this mirrored so I know my files are safe from a hard drive crash. I'm not terribly worried about using my my 500GB of space yet since I haven't had anything near that much space on my laptops and I have always just deleted items as I went along. There is more than enough content on the internet that I look at daily/weekly to replace what I didn't see, so I'll just cycle it out. So first, I simply logged into the web interface by going to the ip address of the device. I then went to the 'Drive Management' tab, click on "Change Drive Type", and change my drive type to "Secure volume (RAID-1)". I get a bunch of warnings which I go through and then I wait. This process takes about an hour or so. I just went out and when I came back it was ready.

After reading a lot of forums and blog posts about this device, i decided I didn't really have any interest in the 'mionet' software or the ability to view all of my files from anywhere. At least not with their software. Perhaps serving them via http isn't out of the question, but it's not on the list and not a priority. I mention this because mionet is one of the ways you access your files, and you are supposed to pop in the Western Digital CD that comes with the drive and install all their stupid software. I'm sure it's useful to someone, but Samba is already installed and running by default, so if you have client for ms attached to your nic, which most windows users do, you can just go to `\\<ip of your mybook\PUBLIC` to view the public share that is on the MyBook by default. This worked great for me. I will merely use sub-folders and permissions from the command line on the box to manage security. So I create a few folders after attaching to the UNC and move a bunch of music/pictures/movies/documents/etc over. This is probably where I ran into my first sad moment. The data transmission wasn't exactly 'blazing fast' as I had hoped. Perhaps 'blazing' isn't what i really expected, but it took a lot longer than I thought. I believe moving about 8GB took probably an hour or 90 minutes. This was another one of those things where I just left for a while and came back. The estimate was a lot and I wasn't gone more than 90 minutes though. Now, this can probably mostly be attributed to the fact that I'm on a simple wireless G network. The MyBook comes equipped with a gigabit nic, however my little wireless hub is only 100 and over standard G wireless, you are limited a lot more.

So, at this time, we need to do a little basic security. Back in the web interface again, I go to the 'General Setup' tab and change the admin password. This is where I ran into what I think was my MOST dissapointing problem with this device. Entering a strong password I regularly use returns this happy message: "Use only letters, numbers, a-z or 0-9".  Oh, how sad. no symbols? Yuck! Ah, well, who knows if I'll even end up using this interface in the end.

Well,  at this point, we *have* accomplished our very first goal. The device does "serve as a storage point for all files I wanted to manually backup."

I've decided to add two more goals to my list:

7. Automate personal administrative tasks.
8. sync my backup data with my s3 account. 

Number one is in reference to servicing the many generic daily tasks i can automate with scripting. I could purchase any box to do these for me, but I'd like to have them run off of this one little device. This will probably be accomplished via some of the other goals, but I think it is a distinct bullet point itself. Two is something I've wanted to do for a long while. I have a large majority of what I consider 'important personal data' backed up to s3 already. Really pictures/movies of my kids, family, etc. I do have them backed up on some DVDs around here as well, but I would like it to be organized digitally, then simply stored on Amazon's S3 for offsite. Storage. Perhaps, eventually, incorporating this with a formalized plan using DVDs or whatever we're burning to at that time. Hard drives are cheap enough, it seems a waste to burn to media, but who knows, and that's in the future and has nothing to do with accomplishing the goal really. =P
More to come, that's enough for now. The fun has barely started!!! =D 