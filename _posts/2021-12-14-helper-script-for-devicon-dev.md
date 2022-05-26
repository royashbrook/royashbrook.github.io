---
layout: post
published: true
title: Helper script for devicon.dev
subtitle: >-
  because copy/paste is too painful!
date: '2021-12-14'
---

I got tired of copying/pasting while adding icons from [devicon.dev](https://devicon.dev/) to my GitHub profile page recently, so I created this little helper script so I could just click on the icons and it would generate some markup for me.

{% gist 64a1333684c40b4d885fb6d353913266 %}

### Some more history on this:
Recently, I 'discovered' that I could add a README.md to my GitHub profile page. I'm quite late on this 'discovery' only coming across it when I went to [someone's profile page](https://github.com/dfinke) and they had a lot of cool things there.

A handful of youtube videos later and I was ready to spin one up. I wanted to add some icons for the dev items and came across the [devicon.dev](https://devicon.dev/) site which had a ton of great icons I could include via CDN.

This was cool, but since I wanted to simply add the img tag, I had to select an icon, go over to the left side, then copy and paste the img tag which wasn't really convenient setup to do this. See below for a dramatic recreation of this activity!

![Animation](https://user-images.githubusercontent.com/7390156/146087974-f3345676-a611-43a0-af16-97fd2c8e11d3.gif)

Maybe not so dramatic.. 

Well... anyway, since I was fiddling around with formatting and things, I ended up going back and forth and back and forth while I figured out which icons I wanted to use, etc. After a bit, I decided that this was kind of a pain and I wished I could just click on the icons I wanted and copy and paste some markdown. So I put together the little js above and ran it in my browser console. It could be tweaked a lot, but as with all things like this, [it may not be worth it](https://xkcd.com/1205/).

If i was passing these through a resizer, I'm guessing I could use normal image tags, but this setup seemed to be similar to what I found on some other profiles and kept all of them in a line, so that's what I went with. It's easy enough to tweak the line that outputs the markdown if needed.

I actually ended up just kind of hand tweaking things on my file rather than using this too much, but it helped me while I was fiddling around and gave me a little entertainment, so maybe it'll help someone else! =)

- [read more on the github docs](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme)
- [great tool to automate this](https://github.com/rahuldkjain/github-profile-readme-generator) - A better way to do this!
- [great article with a bunch of great tools to automate this](https://javascript.plainenglish.io/the-best-readme-generators-for-your-github-profile-ea4f50559d87)
