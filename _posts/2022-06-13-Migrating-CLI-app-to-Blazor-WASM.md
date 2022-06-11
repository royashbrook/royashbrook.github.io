---
layout: post
published: false
title: Migrating a dotnetcore CLI app to Blazor/WASM
date: '2022-06-13'
---
I have an old app written in dotnetcore that i'd like to turn into a static app to run on the web. Seems like a decent candidate for blazor/wasm. Here's the steps I went through to do it!

## A template?

I searched a little for a template and maybe I was using the wrong search terms, but I couldn't seem to find a client only app. I decided to just look for the most basic tutorial figuring that may have what I needed, so ended up [here](https://dotnet.microsoft.com/en-us/learn/aspnet/blazor-tutorial/). And after walking through the steps in that tutorial, i had a client side app. It had a fetch data stub, but that didn't actually go anywhere, so it seemed like this could be a good template candindate for me to just add some input/output stuff to and use for client only.

maybe... i'm still not 100% clear on which part of this is server and which is client =)

I knew from looking through [this](https://docs.microsoft.com/en-us/aspnet/core/blazor/host-and-deploy/webassembly) page previously that i could bundle it up for deployment in some regard. so I added `<RunAOTCompilation>true</RunAOTCompilation>` to my csproj file and ran `dotnet publish -c Release` as shown in that link. I got this:

![image](https://user-images.githubusercontent.com/7390156/173206237-63beb2b2-8389-4dd9-bd6e-faffd946bd89.png)


Fabs... but after a `sudo dotnet workload restore` it seemed to publish. unfortunately, it looks like there are dlls and iis config stuff that is needed rather than some kind of static site that i could put anywhere. 

![image](https://user-images.githubusercontent.com/7390156/173206292-40b833ec-80ce-49f0-905f-894ee99222c9.png)

Onward to try more things. =) After reading a bit more, i came across [this](https://docs.microsoft.com/en-us/azure/static-web-apps/deploy-blazor) link which seemed like it may be what i was looking for.

prior to doing anything on that page though, i thought why not just fire up `dotnet new` and see what was there. i only had a handful of templates installed so but per the help on the screen i could run `dotnet new web --search` for more so i ran `dotnet new web --search | grep blazor` and just read through some of the desriptions. i decided to give `blazorwasmhostedmin` a whirl as i think i read that hosted is what i want.... maybe =) unfortunately `dotnet new --install blazorwasmhostedmin` didn't do it. after a quick google, just need to use a different value in the template list to get the templates installed. `dotnet new -i Toolbelt.AspNetCore.Blazor.Minimum.Templates` did the install and `dotnet new blazorwasmhostedmin -o bwhm` gave me the blazor wasm hosted min app i was looking for.

![image](https://user-images.githubusercontent.com/7390156/173206453-a9c0af34-577c-4a56-a93b-4a12927f39f9.png)

jumping into that dir and running the same publish command seems to look like we have the wasm output i was wanting to see. although i see it's using that framework dir and it's like 15MB for I'm guessing a nothing-burger worth of code. i remember client size being the main reason i didn't pursue blazor/wasm years ago when i first looked at it. i'm sure there are some tricks to tweak this down, and i think moving a cli app to a web workable version, even if it's a little bigger, is probably pretty nice for my use case so, yay.... i think!?

I decided at this point to give that AOT compiler hint a shot because why not take a peak. I had to run another `sudo dotnet workload restore` for this to work, but it did after that. took quite a little bit to actually do it's thing though because i was compiling the client and server project. but i didn't want to spend time stripping that out quite yet, figured i would just see how that went. it took about 2-3 minutes on a m1 mbpro, so not 'zippy' but was fine for one run. at this point i was really just trying to see if i could make a wasm app of any kind and see it published in a folder that i thought i could copy/deploy somewhere. unfortunately this latest change seemed to make a 30MB deployment which was suboptimal.

at this point, i decided to maybe move on with converting my app. seemed like this would work even if i needed some more work. 










I have hosted my blog at various places over the years, but in 2018 i decided to register ashbrook.io and move my blog there. I grabbed an o365 tenant for email and other fun stuff and setup a jekyll site on github pages. that has worked great since june of 2018. recently, i decided i wanted to move back to royashbrook.com. i figured i'd rather have my name in the domain anyway and i have owned that domain for i dunno how long. ashbrook.io seemed cool at the time and i'll probably keep it for family use email and things like that. i like having an o365 tenant for testing, but i thought it might be easier to find my stuff if i used my actual name again.
