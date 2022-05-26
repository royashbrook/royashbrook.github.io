---
layout: post
title: Audit Local User Accounts Using Powershell
---

Here are some ways to audit local user accounts using powershell.

``` powershell
#get local accounts from a list of servers
# get the list, make sure the servers pass a ping check, pull back the account list
# ignore the errors, we only care about the stuff we *can* reach
# uses servers.csv file with a Name column as input
# outputs to accounts.csv

$servers = $null # server list, pulled name and sorted from csv
$pj = $null # ping job
$pr = $null # ping results
$upservers = $null #servers who were pinged successfully
$aj = $null # account job
$ar = $null # account results
$LocalAccounts = $null # local accounts from the machines

$servers = (Import-Csv servers.csv | sort Name | %{ $_.Name } )
$pj = Test-Connection -ErrorAction SilentlyContinue `
    -ttl 5 -Count 1  -ComputerName $servers -AsJob
$null = Wait-Job $pj
$pr = Receive-Job $pj
$upservers = ($pr | ? {$_.StatusCode -eq 0} | % {$_.Address}) 
$aj = Get-WmiObject -ErrorAction SilentlyContinue `
    -Class Win32_UserAccount -Filter "LocalAccount='$True'" `
    -ComputerName $upservers -AsJob
$null = Wait-Job $aj
$ar = Receive-Job $aj
$LocalAccounts = ($ar | select Domain,Name,FullName | Format-Table)

#or dump out to csv instead of var
$ar | select Domain,Name,FullName | Export-Csv -NoTypeInformation -Path accounts.csv
```

Edit:

It's worth noting I found this set of code to manually split the jobs up. I ran it with a cap higher than the total number of machines and it did finish faster.


``` powershell
#baseline comparison of localhost
PS C:\>Measure-Command {$Results = Test-Connection localhost}


Days              : 0
Hours             : 0
Minutes           : 0
Seconds           : 3
Milliseconds      : 109
Ticks             : 31091556
TotalDays         : 3.59855972222222E-05
TotalHours        : 0.000863654333333333
TotalMinutes      : 0.05181926
TotalSeconds      : 3.1091556
TotalMilliseconds : 3109.1556


#just doing a normal list pass in 
PS C:\> Measure-Command { `
>> $Results = Test-Connection -ttl 5 -Count 1 -ErrorAction SilentlyContinue -ComputerName $servers}


Days              : 0
Hours             : 0
Minutes           : 2
Seconds           : 57
Milliseconds      : 906
Ticks             : 1779068137
TotalDays         : 0.0020591066400463
TotalHours        : 0.0494185593611111
TotalMinutes      : 2.96511356166667
TotalSeconds      : 177.9068137
TotalMilliseconds : 177906.8137


#what I actually did when I was working, takes less than half the high time of the normal list
PS C:\> Measure-Command { `
>> $job = Test-Connection -ttl 5 -Count 1 -ErrorAction SilentlyContinue -ComputerName $servers -AsJob
>> $null = Wait-Job $job
>> $Results = Receive-Job $job }


Days              : 0
Hours             : 0
Minutes           : 0
Seconds           : 21
Milliseconds      : 98
Ticks             : 210987558
TotalDays         : 0.0002441985625
TotalHours        : 0.0058607655
TotalMinutes      : 0.35164593
TotalSeconds      : 21.0987558
TotalMilliseconds : 21098.7558


#what i found online when i looked around as i was curious if asjob was parallel. it's about half of the faster one
#inspired by this: https://stackoverflow.com/questions/8781666/run-n-parallel-jobs-in-powershell
PS C:\> Measure-Command {
>> $List = $servers
>> $Jobs = 100
>>
>> Foreach ($PC in $List)
>> {
>> Do
>>     {
>>     $Job = (Get-Job -State Running | measure).count
>>     } Until ($Job -le $Jobs)
>>
>> Start-Job -Name $PC -ScriptBlock { Test-Connection -ttl 5 -Count 1 -ErrorAction SilentlyContinue -ComputerName $PC }
>> Get-Job -State Completed | Remove-Job
>> }
>>
>> Wait-Job -State Running
>> Get-Job -State Completed | Remove-Job
>> $Results = Get-Job
>> }


Days              : 0
Hours             : 0
Minutes           : 0
Seconds           : 13
Milliseconds      : 299
Ticks             : 132993910
TotalDays         : 0.000153928136574074
TotalHours        : 0.00369427527777778
TotalMinutes      : 0.221656516666667
TotalSeconds      : 13.299391
TotalMilliseconds : 13299.391
```
