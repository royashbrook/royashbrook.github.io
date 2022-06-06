---
layout: post
published: false
title: Blog Migration/Update and GitHub Codespaces
date: '2022-06-06'
---
I've decided to migrate all of my blog back to royashbrook.com. Why... well, that just seems more like home for my stuff and i think it's time to move back.

## Blog Migration/Updates

I had hosted my blog at various places over the years, but in 2018 i decided to register ashbrook.io and move my blog there. I grabbed an o365 tenant for email and other fun stuff and setup a jekyll site on github pages. that has worked great since june of 2018. recently, i decided i wanted to move back to royashbrook.com. i figured i'd rather have my name in the domain anyway and i have owned that domain for i dunno how long. ashbrook.io seemed cool at the time and i'll probably keep it for family use email and things like that. i also like having an o365 tenant for testing.

i was also kind of tired of my blog theme. i have been using [https://beautifuljekyll.com/](https://beautifuljekyll.com/) since moving to github pages. I like it, but it was a little heavier than my actual personal style (more plugins than i really wanted/needed) and a little prettier than my personal style. =) To that end, i have switched over to a theme based on [https://andrewhwanpark.github.io/brutalist-blog/](https://andrewhwanpark.github.io/brutalist-blog/). i came across this theme when i was just looking for inspiration. as with many folks using github pages i started out looking through things to maybe be 'inspired' and then basically end up using a theme so you can go back to blogging =P when searching for a link to this blog later on google, i found that brutalist websites are actually a thing. definitely more my style personally. i don't want a brutalist house, but i'm pretty minimal/functional on my design choices for applications. my second choice was [https://p0wex.github.io/Gesko/](https://p0wex.github.io/Gesko/) for a theme, so actually kind of close haha. you can see some other examples [here](https://brutalistwebsites.com/). I'm not sure that my blog is the best example of a brutalist site, but it's pretty minimal with some fun style so I'm down. =)

i would say that the biggest 'helper' task for me in this update was putting my posts into categories. really this just amounts to me putting _posts folders inside of other folders. i just made categories for the decades i have blogged so i could keep some of the 'older' stuff out of the folder i usually opened up to create posts. before this i had everything in one folder, so every time i went to make a new post i had to scrolllllllllllllllllll a lot to get down to the bottom of the github page to add a file or copy the last file or look at the most recent file. now i have the posts in 2000s,2010s,2020s folders, and kept the top level _posts folder for the most recent year or whatever i want to have in it so i have a smaller folder to look through when i go to blog.

i used to use [prose.io](https://prose.io) for editing, but i actually prefer to simply use github itself as i can copy/paste images much easier. have to copy/paste the frontmatter, but that's fine. i used to have subtitles on my posts, but now that i have ditched them the front matter is just the title and date basically.

## GitHub Codespaces

I also thought I'd record my thoughts on codespaces. I received my 'hey! you're in the beta!' email a couple of weeks ago and got my first opportunity to really play around with it during this experience. You can examine this repository to see the implementation of getting it setup. Nothing particularly special, just a standard github pages setup after cloning the template site. Once I had that up, I had a bunch of little changes and organizational changes to do, i decided to do those in codespaces. I actually did play with this a little bit in some other repositories, but i basically just 'opened it and looked at it' kind of like... poked at it weirdly but didn't do anything 😛.

So first things first on this were to get the blog to show up so i could 'develop locally' or at least browse it locally, that is. from some past looking at codespaces, it seemed like this should be pretty turnkey. i haven't actually run jekyll locally in quite some time as i have had things running on github and i would just commit and let the build go or not if i did something funny. as most of my updates were just new blog posts, it was usually fine. so i needed to figure out how to even run it locally, but why do that when i can just search for running it on codespaces. a quick google brought me to [this](https://gist.github.com/jacobdepriest/75f358377d649bb098106c9ddadfd3df) super helpful gist, and that led to me to simply create a little shell script i could run when the codespace popped up.

```text
#!/bin/bash
#chmod +x ./test.sh
gem install bundler jekyll
bundle update
bundle exec jekyll serve
```

this worked like a champ and would give me a little server on http://127.0.0.1:4000/ to hit for dev. I was clicking the 'open in vscode' locally button when i ran this, so it was doing some fun magic to make it work, but it did all just work like a charm for me. i also noticed i could, it seems, create multiple codespaces. this seems like it would be really neat if i didn't want to create a branch, but just wanted to spin up a codespace, fiddle with some things to see, and then turn it off and it all goes away. i guess that's the point. i mean you can already get this similar situation with many hosts by just allowing builds from branches and then your prs can get them and stuff, but this makes the initial testing even easier i suppose than creating that.

i also guess this is bound to an org or a user or something. i noticed i could not open codespaces in some other organizations, but i could open them in all of my own repos. you can read more about github codespaces [here](https://docs.github.com/en/codespaces). I think this is cool if you have older slower machines with good screens or you don't want to worry about infrastructure, but I gotta imagine many people already have laptops and most devs want to have a laptop i think, or at least a machine. probably pretty cool for bringing in people to just work on something as you just have them open it up and their machine doesn't matter. i'm trying to think about how i would feel if i went to a new company and they just had everything setup in the cloud like this and wanted me to have my own machine. i'm guessing that as long as i had one or maybe there was at least some kind of an allowance for one, i would be fine. i haven't checked yet, but i have to wonder if this would make working on a tablet actually possible. would seem like it would, especially if you are ok with that screen size.

i personally prefer to use a larger monitor laptop or a double wide monitor for coding, but anything that can plug into a big monitor should work for this. it actually works quite well. i'll have to test it with some 'non-github pages' things


