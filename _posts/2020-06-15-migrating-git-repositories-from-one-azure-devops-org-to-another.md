---
layout: post
published: true
title: migrating git repositories from one azure devops org to another
image: /img/azuredevops.png
subtitle: 'so easy, even i can do it!'
date: '20200615'
---
I have a lot of projects in azure devops. I started with a handful, but I started by storing each of the customer integrations and other _semi-related_ things into their own projects. Why? Well, largely it is only me generating and maintaining all of this code. However it has recently become a bit unwieldy as I approach 100 projects. Since about 70 of these are *very similar* automation jobs, and quite a few other jobs are related, I decided it was time to cleanup. Fortunately, while I do have some other folks accessing devops for project management purposes, I don't have a lot of pushback when I need to change things like this as long as some guidance is provided. So, for me, this meant I could just mirror the repos I wanted to keep, leave the rest behind, and archive/rename the org itself until I was ready to delete it. I am using git on azure devops.

There were a few other articles online I used, but mostly [this one](https://help.github.com/en/github/creating-cloning-and-archiving-repositories/duplicating-a-repository) covered most things.

I used powershell for this, so first I setup my target projects. In the source I had one repo per project, but now I will have a much smaller number of projects with multiple related repos in each. I'm not including the azure devops extension commands for this, but you can look them up [here](https://docs.microsoft.com/en-us/azure/devops/cli/) if you wish.

I created a dummy folder to work in. I'm not sure if anyone else accidentally creates folders like this in their onedrive folder, but, if so, don't. =) 
``` PowerShell
# you'll need to make a folder to work in, i just used foo
# so run this in whatever folder you you want to work in
md foo
```

Go ahead and login to azure...
``` PowerShell
az login
```

You'll also need the azure devops extension in order to manage repos. You can create all of the repos beforehand, but I didn't want to do that.
``` PowerShell
az extension add --name azure-devops
```

Next I created a couple of little helper functions to make the repos mirror them.
``` PowerShell
# make a repo for a given org, project, and rep name
function mkrep($org, $prj, $rep){
    az repos create --organization $org --project $prj --name $rep
}
# mirror a rep from one site with where the project name matches the rep
# to another site where the project name is prj for that rep
function mirrep($sourcesite,$targetsite,$prj,$rep){
    git clone --bare $sourcesite/$rep/_git/$rep
    cd .\$rep.git\
    git remote set-url --push origin $targetsite/$prj/_git/$rep
    git fetch -p origin
    git push --mirror
    cd ..
}
```

After this, it was pretty simple to just loop and create the repos and then mirror into them. I did things in batches, but they all conformed to this model and i generally did one project at a time so i could just wipe out the repos for that project if there was an issue or typo or something. 
``` PowerShell
$repos = "some,list,of,repos,with,commas,in,between,them"
$myoldorg = "https://dev.azure.com/MyOldOrgName"
$myneworg = "https://dev.azure.com/MyNewOrgName"
$myprj = "MyNewProjectName"
$repos -split "," | %{ mkrep $myneworg $myprj $_ }
$repos -split "," | %{ mirrep $myoldorg $myneworg $myprj $_}
```

You could just do a list with each project and their child projects and do them all at once, but I only had ~100, so it was just as easy for me to batch them by projects. Also lots of other ways to list things and proecess the list, this was just the easiest for me to do with some small ones and it only took a minute of search/replace to get the larger projects into a csv list as well. The really important thing is just a way to create the repos and a way to move items over. I just needed all of the history, but not the work items and things like that so you'll need something extra for that.

Afterwards, I just wiped out the foo folder as well.

