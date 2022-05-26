---
layout: post
published: true
title: 'Switching from Master to Main in Git on Azure Devops '
subtitle: even small changes help
date: '2020-10-15'
---
As with most folks, my git repos started with a default value of 'master' and I wanted to change them to 'main' instead. This would work for any other preferred default branch name. This change was inspired mainly by [this blog post](https://www.hanselman.com/blog/easily-rename-your-git-default-branch-from-master-to-main) by Scott Hanselman and the discussion that surrounded it.

You can review Scott's blog post above for more details on his post. I will also note that I found the specific devops cli command I needed to update the default on [this blog post](https://medium.com/objectsharp/set-default-branch-in-azure-repos-f879ec1509d0). I have about 100 repositories that needed to be updated. I was not in a massive hurry and there are reasons to touch these repositories on a somewhat regular basis over the course of some months. So I did not take an approach of updating everythign all at once and I just did them one at a time as I went along. So far I've done about 10 of them and I expect to finish updating them over the course of the year.

I'm using git on azure devops and have a variety of projects these repositories are spread across. As a change came up for one, I simply ran the following in the folder i was in:

``` PowerShell

#move the master branch to main
git branch -m master main
git push -u origin main

# note that i ran the lines above first and then after making
#   sure things looked ok, i deleted master using the command below
git push origin --delete master

#next I updated the default branch to main for the repo in question
az repos update --project %PROJECT_NAME% --repository %REPO_NAME% --default-branch main

#assuming you are in the folder with the repo and the repo name
#   is the same, you can just run the following which is what I always did
az repos update --repository (split-path .\ -leaf) --default-branch main

#you can validate that the default branch has changed by 
#    looking at the list of repos in the project:
az repos list --project %PROJECT_NAME% --output table

```

This is a small change to make. Even small changes can help if there are enough of them.

> Edit: I did find a need to run this as a report sometimes for all of the projects and all of the repos. I used the following one liner for that. You can pretty it up and merge the output if needed, but I just needed something i could look at occassionally. `(az devops project list | convertfrom-json).value | %{ az repos list --project $_.name --output table }` Note that you'll need to be logged in and have appropriate permissions to get these results.

