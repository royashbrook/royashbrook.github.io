---
layout: post
published: true
title: ConvertFrom-CSV header weirdness parsing IISLogs with Powershell
---
So, today I was parsing a bunch of IIS logs. Simple process, no? 

As usual, hit up google to avoid recreating the wheel searching for parsing iis logs with powershell. End up [here](https://bentaylor.work/2016/09/parsing-iis-logs-to-powershell-objects/) which looks good. Grab the function, but it doesn't work and gives me an error like this over and over again:

```powershell
PS C:\me> dir '*.log' | Convert-IISLogsToObject | Sort-Object c-ip -Unique | Select-Object date, time, s-ip, c-ip, cs-uri-stem, cs-username | Format-Table
ConvertFrom-Csv : Cannot validate argument on parameter 'Header'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.
At line:14 char:116
+ ... tern '^#' -NotMatch | ConvertFrom-Csv -Delimiter ' ' -Header $headers
+                                                                  ~~~~~~~~
    + CategoryInfo          : InvalidData: (:) [ConvertFrom-Csv], ParameterBindingValidationException
    + FullyQualifiedErrorId : ParameterArgumentValidationError,Microsoft.PowerShell.Commands.ConvertFromCsvCommand
```

That seems odd, I look at the function code and it looks fine to me, but who knows. So I just whip up something like this really quick:

```powershell
PS C:\me> $h = (gc .\ex180302.log | ?{$_.substring(0,9) -eq '#Fields: '} | select -first 1).substring(9).split(' ')
PS C:\me> $h
date
time
s-ip
cs-method
cs-uri-stem
cs-uri-query
s-port
cs-username
c-ip
cs(User-Agent)
sc-status
sc-substatus
sc-win32-status
```

OK, so looks good. I try the following for a single file and _still_ get an error:

```powershell
PS C:\me> gc .\ex180302.log | ConvertFrom-Csv -Delimiter " " -Header $h
ConvertFrom-Csv : Cannot validate argument on parameter 'Header'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.
At line:1 char:60
+ gc .\ex180302.log | ConvertFrom-Csv -Delimiter " " -Header $h
+                                                            ~~
    + CategoryInfo          : InvalidData: (:) [ConvertFrom-Csv], ParameterBindingValidationException
    + FullyQualifiedErrorId : ParameterArgumentValidationError,Microsoft.PowerShell.Commands.ConvertFromCsvCommand
```

What gives? Am I mistaken than this should be a string array? Seems unlikely since the other code seemed to be written for one, but powershell is 'flexible' and can sometimes do weird things so I check.

```powershell
PS C:\me> get-help convertfrom-csv

NAME
    ConvertFrom-Csv

SYNTAX
    ConvertFrom-Csv [-InputObject] <psobject[]> [[-Delimiter] <char>] [-Header <string[]>]  [<CommonParameters>]

    ConvertFrom-Csv [-InputObject] <psobject[]> -UseCulture [-Header <string[]>]  [<CommonParameters>]


ALIASES
    None


REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
        -- To view the Help topic for this cmdlet online, type: "Get-Help ConvertFrom-Csv -Online" or
           go to https://go.microsoft.com/fwlink/?LinkID=135201.
```

OK, so it needs to be a string array. I can see data in the $h. I figure maybe $h is not a string array or something?

```powershell
PS C:\me> $h |gm


   TypeName: System.String

Name             MemberType            Definition
----             ----------            ----------
Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), int IComparable.CompareTo(System.Object obj), int IComparable[string].CompareTo(string other)
Contains         Method                bool Contains(string value)
CopyTo           Method                void CopyTo(int sourceIndex, char[] destination, int destinationIndex, int count)
EndsWith         Method                bool EndsWith(string value), bool EndsWith(string value, System.StringComparison comparisonType), bool EndsWith(string value, bool ignoreCase, cultureinfo culture)
Equals           Method                bool Equals(System.Object obj), bool Equals(string value), bool Equals(string value, System.StringComparison comparisonType), bool IEquatable[string].Equals(string other)
GetEnumerator    Method                System.CharEnumerator GetEnumerator(), System.Collections.IEnumerator IEnumerable.GetEnumerator(), System.Collections.Generic.IEnumerator[char] IEnumerable[char].GetEnumerator()
GetHashCode      Method                int GetHashCode()
GetType          Method                type GetType()
GetTypeCode      Method                System.TypeCode GetTypeCode(), System.TypeCode IConvertible.GetTypeCode()
IndexOf          Method                int IndexOf(char value), int IndexOf(char value, int startIndex), int IndexOf(char value, int startIndex, int count), int IndexOf(string value), int IndexOf(string value, int startIndex), int IndexOf(string value, int startIndex, int count), int IndexOf(string value, System.StringComparison comparisonType), int IndexOf(string value, int startIndex, System.StringComparison comparisonType), int IndexOf(string value, int startIndex, int count, System.StringComparison comparisonType)
IndexOfAny       Method                int IndexOfAny(char[] anyOf), int IndexOfAny(char[] anyOf, int startIndex), int IndexOfAny(char[] anyOf, int startIndex, int count)
Insert           Method                string Insert(int startIndex, string value)
IsNormalized     Method                bool IsNormalized(), bool IsNormalized(System.Text.NormalizationForm normalizationForm)
LastIndexOf      Method                int LastIndexOf(char value), int LastIndexOf(char value, int startIndex), int LastIndexOf(char value, int startIndex, int count), int LastIndexOf(string value), int LastIndexOf(string value, int startIndex), int LastIndexOf(string value, int startIndex, int count), int LastIndexOf(string value, System.StringComparison comparisonType), int LastIndexOf(string value, int startIndex, System.StringComparison comparisonType), int LastIndexOf(string value, int startIndex, int count, System.StringComparison comparisonType)
LastIndexOfAny   Method                int LastIndexOfAny(char[] anyOf), int LastIndexOfAny(char[] anyOf, int startIndex), int LastIndexOfAny(char[] anyOf, int startIndex, int count)
Normalize        Method                string Normalize(), string Normalize(System.Text.NormalizationForm normalizationForm)
PadLeft          Method                string PadLeft(int totalWidth), string PadLeft(int totalWidth, char paddingChar)
PadRight         Method                string PadRight(int totalWidth), string PadRight(int totalWidth, char paddingChar)
Remove           Method                string Remove(int startIndex, int count), string Remove(int startIndex)
Replace          Method                string Replace(char oldChar, char newChar), string Replace(string oldValue, string newValue)
Split            Method                string[] Split(Params char[] separator), string[] Split(char[] separator, int count), string[] Split(char[] separator, System.StringSplitOptions options), string[] Split(char[] separator, int count, System.StringSplitOptions options), string[] Split(string[] separator, System.StringSplitOptions options), string[] Split(string[] separator, int count, System.StringSplitOptions options)
StartsWith       Method                bool StartsWith(string value), bool StartsWith(string value, System.StringComparison comparisonType), bool StartsWith(string value, bool ignoreCase, cultureinfo culture)
Substring        Method                string Substring(int startIndex), string Substring(int startIndex, int length)
ToBoolean        Method                bool IConvertible.ToBoolean(System.IFormatProvider provider)
ToByte           Method                byte IConvertible.ToByte(System.IFormatProvider provider)
ToChar           Method                char IConvertible.ToChar(System.IFormatProvider provider)
ToCharArray      Method                char[] ToCharArray(), char[] ToCharArray(int startIndex, int length)
ToDateTime       Method                datetime IConvertible.ToDateTime(System.IFormatProvider provider)
ToDecimal        Method                decimal IConvertible.ToDecimal(System.IFormatProvider provider)
ToDouble         Method                double IConvertible.ToDouble(System.IFormatProvider provider)
ToInt16          Method                int16 IConvertible.ToInt16(System.IFormatProvider provider)
ToInt32          Method                int IConvertible.ToInt32(System.IFormatProvider provider)
ToInt64          Method                long IConvertible.ToInt64(System.IFormatProvider provider)
ToLower          Method                string ToLower(), string ToLower(cultureinfo culture)
ToLowerInvariant Method                string ToLowerInvariant()
ToSByte          Method                sbyte IConvertible.ToSByte(System.IFormatProvider provider)
ToSingle         Method                float IConvertible.ToSingle(System.IFormatProvider provider)
ToString         Method                string ToString(), string ToString(System.IFormatProvider provider), string IConvertible.ToString(System.IFormatProvider provider)
ToType           Method                System.Object IConvertible.ToType(type conversionType, System.IFormatProvider provider)
ToUInt16         Method                uint16 IConvertible.ToUInt16(System.IFormatProvider provider)
ToUInt32         Method                uint32 IConvertible.ToUInt32(System.IFormatProvider provider)
ToUInt64         Method                uint64 IConvertible.ToUInt64(System.IFormatProvider provider)
ToUpper          Method                string ToUpper(), string ToUpper(cultureinfo culture)
ToUpperInvariant Method                string ToUpperInvariant()
Trim             Method                string Trim(Params char[] trimChars), string Trim()
TrimEnd          Method                string TrimEnd(Params char[] trimChars)
TrimStart        Method                string TrimStart(Params char[] trimChars)
Chars            ParameterizedProperty char Chars(int index) {get;}
Length           Property              int Length {get;}
```

Looks like a string to me.... hmmm.

Let's try making *sure*, _sure_ it is a string. I'll skip some extra troubleshooting and just show you what did finally work:

```powershell
PS C:\me> $h = (gc .\ex180302.log -TotalCount 4  | Select -First 1 -Skip 3) -replace "#Fields: " | out-string
PS C:\me gc .\ex180302.log | ConvertFrom-Csv -Delimiter " " -Header $h.split(" ") | select -first 10 | ft *
```

So what gives? I dunno. Maybe there is something funny with the characters in the header line? I wouldn't think so because it is just a basic IIS log, but who knows.

```powershell
PS C:\me> [char[]]$h | %{"Char:{0},Number:{1}" -f $_,[int][char]$_}
Char:d,Number:100
Char:a,Number:97
Char:t,Number:116
Char:e,Number:101
Char: ,Number:32

## snipped...

Char:-,Number:45
Char:s,Number:115
Char:t,Number:116
Char:a,Number:97
Char:t,Number:116
Char:u,Number:117
Char:s,Number:115
Char: ,Number:32
,Number:13
Char:
,Number:10
```

So, there are some characters we don't really want in there. I would think/hope that powershell would fix them automatically somewhere, but doesn't look like it. So while the previous item I mentioned did _work_, it did not actually work right. If you run it on a log you'll see the header rows are actually spaced incorrect. This seems to be due to the char 13 and 10 at the end of the line. So this line actually worked fine to set the headers `gc .\ex180302.log | ConvertFrom-Csv -Delimiter " " -Header ($h.split(' ') | ?{$_.trim().length -ne 0}) | select -first 10 | ft *`

Of course there are several ways to format the commands. But this was my actual final used code:

```powershell
$l = "*.log"
$h = (gc (gci $l | select -f 1) -total 4)[3] -replace '#Fields: ' -split ' ' | ?{$_.trim().length -ne 0}
$d = Import-Csv (gci "ex1803*.log") -Delimiter ' ' -Header $h | ?{$_.date -notmatch "#"}
$d | group date, cs-uri-stem | select Count, Name | sort count -Descending
$d | group date, c-ip | select Count, Name | sort count -Descending
$d | Out-GridView
```

Unlike ConvertFrom-CSV, Import-CSV does not discard lines with a # in front, so you have to filter them out. In my case, i was just looking for something to see unique counts by uri,date, and clientip. Basically who is calling what and how frequently. =) Also, dump it out to the gridview to look through while I am doing this.