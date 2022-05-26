---
layout: post
published: true
title: Add a work-item to devops with storypoints from CLI
subtitle: 'CLI, CLI, CLI'
date: '2020-07-01'
---
TLDR; `az boards work-item create --type "user story" --title "some title" --project "project name" --fields "Microsoft.VSTS.Scheduling.StoryPoints=1"` That's the command. =)

## Can I have a little more info?

Sure, first of all, you'll need to have the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) installed. Then you'll need to have the [Azure DevOps CLI](https://docs.microsoft.com/en-us/azure/devops/cli/?view=azure-devops) installed. Then you'll need to be logged in and able to use it. Here are some simple commands to check once you are good to go.

```PowerShell

# login
az login

# list your accounts, if you have multiple
az account list

# set your subscription, if you have multiple and want to change from default
az account set --subscription "some subscription name from above"

# list projects
az devops project list --output table
# i don't create projects from CLI, but you'll need at least one, so go setup one in GUI if needed.

```

I normally was just creating the work-items and then opening them, but a lot of times the only thing I wanted to open them for *right then* was to put in the story points. I would later go back into the GUI and put in description information and all of the story points.

```PowerShell

# create user story, and open it
az boards work-item create --type "user story" --title "some title" --project "project name" --open

# create another user story, and just put story points on it
az boards work-item create --type "user story" --title "some other title" --project "project name" --fields "Microsoft.VSTS.Scheduling.StoryPoints=1"

```

If you were using an epic or feature, you would use the `Microsoft.VSTS.Scheduling.Effort` field instead, at least assuming you just want to add the effort value in. And also change the `--type` value.

You can see the full list of default fields [here](https://docs.microsoft.com/en-us/rest/api/azure/devops/wit/fields/list?view=azure-devops-rest-5.0), although if you add/change things it'll be different. That page also has the http get command to get details for your particular organization and project.

> That's a lot to type out crazy man!

I hear you. But with [windows terminal autocomplete](https://github.com/microsoft/terminal/issues/1341) it actually is not a big deal after typing it once. You can create helper functions if you want, but I generally just type it out myself. This is usually something that I personally only do if I am in the middle of something else and want to toss a work-item in really quick, but want to put my initial sizing on it. So important enough to record right then so I can put in more details later, but not so important I want to stop what I'm doing and use `--open` to actually add more information right at that moment.