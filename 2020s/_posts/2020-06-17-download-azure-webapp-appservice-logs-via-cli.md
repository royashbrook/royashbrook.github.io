---
layout: post
published: true
title: Download Azure WebApp / AppService Logs via CLI
subtitle: I better write this down...
date: '2020-06-17'
image: /img/makeanote.png
---
This seems pretty easy, but just in case you have multiple subscriptions and multiple apps on a single app service, this seemed to be the easiest way to get the logs using the way I wanted to get them. This also covers some very minor things that I always forget about like listing/switching subs and listing the webapps themselves. Obviously once you have the id (or any of the other ways to query and download) you can use those commands as well.

``` PowerShell
# login
az login

# list subscriptions
az account list --output table

# above should give you a sub id
az account set --subscription yoursubidgoeshere

# list the webapp names and their ids
az webapp list | ConvertFrom-Json | fl name,id

# download the webapp logs you want to see
az webapp log download --ids "put the long id value from the last command here"
```

Note that you can also use resource group names and friendly names if you have them, but I am not listing those commands here. Above will download to webapp_logs.zip, at least it did for me. In my case this is an iis app so it downloaded logs in that format. If you are running the app with different stack settings, YMMV.