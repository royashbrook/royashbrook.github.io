---
layout: post
published: true
title: Publishing Svelte app to Azure Web App using Azure Devops CI/CD
subtitle: 'super, duper, simple'
date: '2020-10-28'
---
While I love github, I currently use Azure Devops at work for a variety of reasons. I also use svelte for some sites. I wanted to use the CI/CD pipeline feature to publish a svelte site to a test azure web app anytime there was a commit. I didn't even want to build, I just simply wanted to deploy the public folder to the same site anytime there was a commit. Here is the YAML I used:

```yaml
trigger:
- main

jobs:
- job: Linux
  pool:
    vmImage: 'ubuntu-latest'
  steps:
  - task: FtpUpload@2
    inputs:
      credentialsOption: 'inputs'
      serverUrl: 'myurl'
      username: 'myuser'
      password: 'mypass'
      rootDirectory: 'public'
      filePatterns: '**'
      remoteDirectory: '/site/wwwroot'
      enableUtf8: true
      clean: true
      preservePaths: true
      trustSSL: true
```

This seems to do the trick. 👍 Some more info below for context.

## A little background

The site in question has a relatively small user community and is just hosted on the free tier using the \*.azurewebsites.net url. Currently I manually deploy it from vscode by simply right clicking on the folder  and selecting Deploy to Web App. This is actually fine given the limited number of production deployments, but it's very tedious during development. Recently I started working on a side project and was using netlify to show preview builds on PRs. That worked great and I wanted to do that as well.

## So... use netlify?

Yes, this was my original thought as well. Main issue is the repo is on azure devops, but I could move it to a private repo on github as we have some of those anyway. I did this, got it working, and then came across one issue for doing this that I should have remembered. We use azure ad and msal.js for authentication on this site using redirection. This keeps the user experience streamlined and none of the other methods I've floated have been as well received so that's where we are.

## Why does that matter?

Using msal.js with redirects requires you to use the same URL. So to solve this issue I setup branch builds on netlify and authorized my testing netlify url and things were all peachy. I already have a test app and this works fine. But at this point I had already been thinking about the Azure Devops CI/CD. Couldn't I just do what I was doing there?

## Back to azure devops

I have a lot of other dependencies on devops and azure in general, so why fracture the solution space like that? Plus, i didn't see that I could deploy on netlify using every commit. Now, maybe that's too much to deploy, but it was a thing I noticed. The thing I liked about netlify on the other project was getting all the PR based preview sites to look at and I would basically have to just do a branch site on netlify which is kind of what I was thinking I would have with devops anyway. Just deploying over the same site repeatedly. This works for me as it automates what I was already doing and solves my real issue of simply not wanting to manually deploy the site. I can actually test the site locally for most things, but it's nice to deploy it if a change requires some user testing. 

## Any issues with that?

This was my first experience with Azure Devops build pipelines. So I ran into an issue getting npm to actually build the svelte project. After fiddling around with this for a bit, I just removed the public/build folder from gitignore and setup the task as shown above. For production deployments, I will still just right click deploy for now as that wasn't a big issue. In the future, I'd like to get things more automated, but it is just me working on this, so this seems good enough for now.

## notes on the YAML
I couldn't find a full YAML file example so I thought I'd post this one. I found the steps and some of the items, but not just a pasted YAML file someone was actually using. Obviously I have removed the credential and site info, but you can get your own info by going in azure portal and getting the publishing profile for a specific web app. I just saved it as xml, auto formatted it in vscode, and copied the values from the ftp publishing section. I'm triggering off of the branch main, which is what is set up top. I am grabbing everyting in the public folder, putting in in /site/wwwroot (which came from the URL in the publishing profile) and then I checked all of the boxes I have true for. Basically i want utf8, to wipe the folders, preserve the paths (so it'll create subfolders if the wildcard shows files with subfolder paths) and trustSSL since I'm deploying to azure. 


