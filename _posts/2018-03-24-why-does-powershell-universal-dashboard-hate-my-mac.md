---
layout: post
published: true
title: Why does PowerShell Universal Dashboard hate my Mac?
subtitle: >-
  What is this "Microsoft.Extensions.Logging.Filter" and why is it causing me
  issues?
image: 'https://d30y9cdsu7xlg0.cloudfront.net/png/140281-200.png'
---
So recently I was looking for dashboarding _things_ and powershell _things_ at the same time, and I came across a tool called [PowerShell Universal Dashboard](https://www.gitbook.com/book/adamdriscoll/powershell-universal-dashboard/details). You can follow that link or search around to get some info on it. It is a pretty slick dashboarding tool. I think especially nice for some dashboard prototyping if you are into powershell.

The tool was slick and fun to play with, but when I went to load it on my Mac, I got something like the following error:

```powershell
Exception while loading assembly Microsoft.Extensions.Logging.Filter:

System.IO.FileNotFoundException: Could not load file or assembly '/Users/royashbrook/.local/share/powershell/Modules/UniversalDashboard/1.5.0/netcoreapp2.0/Microsoft.Extensions.Logging.Filter.dll'. The system cannot find the file specified.

File name: '/Users/royashbrook/.local/share/powershell/Modules/UniversalDashboard/1.5.0/netcoreapp2.0/Microsoft.Extensions.Logging.Filter.dll'
   at System.Runtime.Loader.AssemblyLoadContext.LoadFromPath(IntPtr ptrNativeAssemblyLoadContext, String ilPath, String niPath, ObjectHandleOnStack retAssembly)
   at System.Runtime.Loader.AssemblyLoadContext.LoadFromAssemblyPath(String assemblyPath)
   at PowerShellProTools.UniversalDashboard.Server.OnAssemblyResolve(AssemblyLoadContext assemblyLoadContext, AssemblyName assemblyName) in D:\a\1\s\PowerShellProTools.UniversalDashboard\Server\Server.cs:line 177
```

So after some fiddling, I found that the module simply did not include the referenced file above. I should say lots of fiddling that did not really need to happen since the problem is stated pretty clearly in the error message. It can't find the file! The really weird thing is that the package for this *was* installed on my machine. Here's a snip:

```powershell
PS /Users/royashbrook/.local/share/powershell/Modules/UniversalDashboard/1.5.0> Get-Package | where name -like "*filter*" | fl *                                                                          
TagId                      : Microsoft.Extensions.Logging.Filter#1.1.2
IsPatch                    : 
PropertyOfSoftwareIdentity : PropertyOfSoftwareIdentity
FastPackageReference       : $L3Vzci9sb2NhbC9zaGFyZS9QYWNrYWdlTWFuYWdlbWVudC9OdUdldC9QYWNrYWdlcy9NaWNyb3NvZnQuRXh0ZW5zaW9ucy5Mb2dnaW5nLkZpbHRlci4xLjEuMi9NaWNyb3NvZnQuRXh0ZW5zaW9ucy5Mb2dnaW5nLkZpbHRlci4
                             xLjEuMi5udXBrZw==\TWljcm9zb2Z0LkV4dGVuc2lvbnMuTG9nZ2luZy5GaWx0ZXI=\MS4xLjI=\atestVersion,tags,deve
ProviderName               : NuGetntDependency}}
Source                     : /usr/local/share/PackageManagement/NuGet/Packages/Microsoft.Extensions.Logging.Filter.1.1.2/Microsoft.Extensions.Logging.Filter.1.1.2.nupkg
Status                     : Installednuget:Microsoft.Extensions.Logging.Abstractions/1.1.2, 
SearchKey                  : Microsoft.Extensions.Logging.Filter.1.1.2 
FullPath                   : Microsoft.Extensions.Logging.Filter.1.1.2.nupkg/net_library_eula_enu.htm...}
PackageFilename            : Microsoft.Extensions.Logging.Filter.1.1.2.nupkg
FromTrustedSource          : True
```

So I didn't go digging around in the server.cs to figure out exactly why, but I'm guessing this is just a missing file. The odd thing is it is also missing on my windows PC but it works there just fine. Maybe on PC these things are loaded into the GAC and the assembly resolution looks there but it won't do that on a Mac.

Regardless, I didn't really worry about this much as I didn't mind just running it on my PC. But I happened to visit a friend and we were discussing it and he *also* had a Mac and we ran into the same problem. Long story short, you can just copy the file that is missing.

Below is how I found the folders for the module and the package. Then it was just a matter of copying the missing dll to the path the error said it was looking for and everything worked perfect.

```powershell
PS /Users/royashbrook/.local/share/powershell/Modules/UniversalDashboard/1.5.0> Get-Module UniversalDashboard | fl name,modulebase                                                                        


Name       : UniversalDashboard
ModuleBase : /Users/royashbrook/.local/share/powershell/Modules/UniversalDashboard/1.5.0



PS /Users/royashbrook/.local/share/powershell/Modules/UniversalDashboard/1.5.0> Get-Package Microsoft.Extensions.Logging.Filter | fl source,status,name                                                   


Source : /usr/local/share/PackageManagement/NuGet/Packages/Microsoft.Extensions.Logging.Filter.1.1.2/Microsoft.Extensions.Logging.Filter.1.1.2.nupkg
Status : Installed
Name   : Microsoft.Extensions.Logging.Filter
```

After this I was able to run all of the fun examples, as long as they didn't need windows specific items. If there is one thing I would suggest modifing in the examples it would be to make them cross platform as this seems like it is intended to be. It was a pain that none of the fun examples for machine status etc worked. But the ones that did sure were pretty. =)