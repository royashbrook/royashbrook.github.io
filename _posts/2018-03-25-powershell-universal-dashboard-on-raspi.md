---
layout: post
published: true
title: Powershell Universal Dashboard on RasPi
image: >-
  https://3.bp.blogspot.com/_IpV86ZyXqlA/Swotc0djuDI/AAAAAAAABIE/QSFVhmYh8Ug/s400/spongebob-1.jpg
subtitle: The Ultimate Universal Excitement!!!
---
https://poshtools.com/powershell-universal-dashboard/

I recently blogged about getting this to work on my Mac. It seems the same issue with the missing dll exists on my RasPi as well. So to get things working I did the following:

```shell
sudo pwsh
```

```powershell
Install-Module UniversalDashboard -AllowPrerelease
```

After this, I tried to run a dashboard like so and got the error shown below.

```powershell
PS /home/pi> Start-UDDashboard -Port 8001 -Dashboard (
>>     New-UDDashboard -Title "Chuck Norris Jokes" -Content {
>>         New-UDRow  -Columns {
>>             New-UDColumn -Size 12 -Endpoint {
>>                 $Joke = Invoke-RestMethod http://api.icndb.com/jokes/random
>>
>>                 New-UDCard -Title "Joke #$($Joke.value.id)" -Text $Joke.value.joke
>>             } -AutoRefresh -RefreshInterval 5
>>         }
>>     }
>>   )  -Wait
Exception while loading assembly Microsoft.Extensions.Logging.Filter:

System.IO.FileNotFoundException: Could not load file or assembly '/usr/local/share/powershell/Modules/UniversalDashboard/1.5.1/netcoreapp2.0/Microsoft.Extensions.Logging.Filter.dll'. The system cannot find the file specified.

File name: '/usr/local/share/powershell/Modules/UniversalDashboard/1.5.1/netcoreapp2.0/Microsoft.Extensions.Logging.Filter.dll'
   at System.Runtime.Loader.AssemblyLoadContext.LoadFromAssemblyPath(String assemblyPath)
   at PowerShellProTools.UniversalDashboard.Server.OnAssemblyResolve(AssemblyLoadContext assemblyLoadContext, AssemblyName assemblyName) in D:\a\1\s\PowerShellProTools.UniversalDashboard\Server\Server.cs:line 182


Hosting environment: Production
Content root path: /usr/local/share/powershell/Modules/UniversalDashboard/1.5.1/netcoreapp2.0/../client
Now listening on: http://0.0.0.0:8001
Application started. Press Ctrl+C to shut down.
```

So this is similar to what I saw in the past on the mac and when I was fiddling with docker. I'm guessing this file just isn't included for some reason in the module and on windows it must autoresolve but doesn't on linux/mac type things. Regardless of the cause, I already have this file on my Mac, so I just scp'd it over to my `/home/pi` folder and finally moved it to the location it was looking for it with `mv *.dll /usr/local/share/powershell/Modules/UniversalDashboard/1.5.1/netcoreapp2.0/`

After this, everything worked dandy. It is worth noting that as soon as this starts up, you'll need to enter your license information immediately. I believe I could copy the license file over and it would work as well, but I have just been activating things when I start them up.

Here are the samples I ran to validate all was working.

```powershell
#Sample chart with random numbers
$pud0 = { 
    New-UDDashboard -Title "Charts" -Content { 
        New-UDMonitor -Title "Downloads per second" -Type Line -DataPointHistory 20 -RefreshInterval 1 -Endpoint {
             Get-Random -Minimum 0 -Maximum 10 | Out-UDMonitorData
        } 
    }
}
#Sample content rotated from another source
$pud1 = { 
	New-UDDashboard -Title "Chuck Norris Jokes" -Content {
		New-UDRow  -Columns {
			New-UDColumn -Size 12 -Endpoint {
				$Joke = Invoke-RestMethod http://api.icndb.com/jokes/random
				New-UDCard -Title "Joke #$($Joke.value.id)" -Text $Joke.value.joke
			} -AutoRefresh -RefreshInterval 5
		}
	}
}
#Sample larger page script pulled directly from github
$pud2 = (iwr "https://raw.githubusercontent.com/ironmansoftware/universal-dashboard/master/examples/poshud.ps1").Content.Replace(" -Wait","")

Start-UDDashboard -Content $pud0 -Port 8080
Start-UDDashboard -Content $pud1 -Port 8081
Invoke-Expression $($pud2) #lots of commands in this one

#now we can go look at our stuff


#to stop all of the dashboards
get-uddashboard|stop-uddashboard
```
