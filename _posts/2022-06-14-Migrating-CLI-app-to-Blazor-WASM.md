---
layout: post
published: true
title: Migrating a dotnetcore CLI app to Blazor/WASM
date: '2022-06-14'
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

i must say i find the documentation pretty annoying when i think this should be a super easy thing to do. doing a dotnet publish on the basic app didn't actually create an index.html or anything in the folders it said. and looking at the section for deployment strategies, you see something like this:

![image](https://user-images.githubusercontent.com/7390156/173207409-f52f7713-f796-4b6b-98e9-488ebb5e2ce0.png)

and when you go to that section you see this:

![image](https://user-images.githubusercontent.com/7390156/173207426-adc649d1-31e5-4954-8b6c-451ecd2c3455.png)

which would lead you to believe if you did a publish, that folder would have all the stuff you need in it, but it does not. Fortunately, down the page a bit, we find a link to [this](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) and *THAT* actually seems to be what i really want. i feel like there is more work to be done, but i think that link is probably going to be the good one to 'start' with as i really just wanted to put this site on github pages anyway =P

## conversion!

the application I'm converting is [here](https://github.com/royashbrook/CLI-PasswordHasher). it's a small tool i wrote to generate the password hash used by aspnet identity, but without a user object. why does this tool exist, well.... it's a long story that has to do with a staged update of a very legacy system over to a newer version of dotnet. this particular system had user accounts in there, but we generate the passwords and provide them manually. when moving to dotnet core it was really setup to allow users to do that, but we didn't want that. so we ripped all of that stuff out, and i wrote this tool to allow you to generate the same hash, but without having to have a user object tied to it which was part of the default ms implementation.

so for a 'web version' the idea was to just have a text box to put the password in, and a hash will come out. i believe the cli has a way to check a hash as well with a password. so put in password and hash and it will say if they match, i'm not sure i'm going to implement that here as that was more a troubleshooting feature for me when i was working on it back then.

so, to start i went ahead and cloned the blazor repo i mentioned above with `gh repo clone SteveSandersonMS/BlazorOnGitHubPages` and run `dotnet watch` to validate that it works and it does. i stripped out all of the pages i didn't need and copied the class files in from the other project and add the dependencies. added a dialogue box for the input and a button that ran the function call in the cli version to the home page. a little razor variable binding, and i was off to the races. i also updated the builder job, moved the repo to a new organization on github and setup github pages. there are lots of ways to host the output, but i'm just copy/passting what was in the original project for that setup as that works fine for me. i also switched over to [https://simplecss.org/](https://simplecss.org/) for css, which is what i have been using lately for my initial setup.

a full compare from the fork to the deployed code is available [here](https://github.com/BlazorWASM-PasswordHasher/BlazorWASM-PasswordHasher/compare/6b8c30ee6f7a26ed9be2ca5777aecab39d8e5716...c69fcc7e68bcb204db6a3ffe1cab13eb0fcaa3af)!

## deploy

src: [[here](https://github.com/BlazorWASM-PasswordHasher/BlazorWASM-PasswordHasher)](https://github.com/BlazorWASM-PasswordHasher/BlazorWASM-PasswordHasher)
web: [https://blazorwasm-passwordhasher.github.io/BlazorWASM-PasswordHasher/](https://blazorwasm-passwordhasher.github.io/BlazorWASM-PasswordHasher/)

screenshot:

<img width="613" alt="image" src="https://user-images.githubusercontent.com/7390156/173565596-90f81799-ffdd-4fce-8229-de3fbf1730f1.png">

## next?

after i finished this, it ocurred to me that i probably should have just made this more generic so it was more like wrapping a call to the original main. that would make it easier to copy/paste drop in anothe executable basically with minimal changes. perhaps another time as i have no other cli tools waiting on this functionality.

There are a couple of things I may want to go back and fiddle with. making the call async would be nice so we could put a spinning wheel up. also i think i need to preload the css as i ran into situations where sometimes it was not coming down as every once in a while i would get a page but the css wouldn't show up beforehand. i'd also like to shorten that URL, but it'll work for now. =)

This was purpose built to solve a very specific problem years ago, so it's not *wayyyy* up there on my priority list. i am betting someone has already probably done this in a much nicer way, but i wanted to go through the exercise. nice to take a dotnet cli app and run it directly in the browser with basically no real code changes. 
