---
layout: post
published: true
title: How big is all of the data I have deleted in Exchange 2010
image: 'https://pbs.twimg.com/media/CI2Wzz9UkAAP-6f.jpg'
---
If you'd like more information on the Recoverable Items folder, check here:
https://technet.microsoft.com/en-us/library/ee364755(v=exchg.141).aspx

If you'd like to see how big those items are, you can use something like the script below. I have a little helper function in their to convert the text value of the size to bytes, then i can sum it up or whatever is needed.

In this case, I can go back and look at the recoverable items info, the mailbox info, or just look at the final number which is a sum in GB.

``` powershell
$s = "servername"
$pss = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri "http://$s/PowerShell/" -Authentication Kerberos
Import-PSSession $pss
$mb = get-mailbox -se $s
$ri = $mb | Get-MailboxFolderStatistics | where{$_.name -eq "Recoverable Items"}
function tb($t){[int]($t.split('(')[1].split(' ')[0].replace(",",""))}
$sz = $ri|select identity,@{n="mb";e={ ([decimal](tb($_.FolderAndSubfolderSize))/1024/1024) } }
#GB total
[math]::round(( $sz | measure mb -sum).sum/1024,2)
```