---
layout: post
published: true
title: Add Global Admin Directory Role in Azure AD via Powershell CLI
image: >-
  https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTKL5axUlIpQIy7COmduxy_qFTpZAAd6yCS5vrv3wRGHwica154bQ
subtitle: 'RBAC, why do you hate me?'
---
Note that this won't work in the azure portal powershell console or in a docker container as those do not seem to be able to let you login to the azure ad interactively.

I was able to do it locally in powershell by installing the azuread tools and running commands like this

``` powershell
Install-Module -Name AzureAD -Force #if you don't have this already
connect-azuread #you’ll have to login here with the popup window
$user = Get-AzureADUser -SearchString "emailaddress"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'Company Administrator'}
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser #show before
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $user.ObjectId
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser #show after

# if you want to remove someone, it's like this
Remove-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -MemberId $user.ObjectId
```

Note [here](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles-azure-portal) where it is noted that:

> In Microsoft Graph API, Azure AD Graph API, and Azure AD PowerShell, this role is identified as "Company Administrator". It is "Global Administrator" in the Azure portal.

This matters because we are actually wanting to add someone as a Global Administrator so they can add Application Accounts and the only way that seems to work is if we give them Global Administrator rights.

Thanks to [Dennis](http://www.thrivefast.com/) and [Shawn](http://shawnweisfeld.com/) for helping on this!
