---
layout: post
title: How to write a basic sniffer in PowerShell
---

So I [recently](http://www.drowningintechnicaldebt.com/RoyAshbrook/archive/2013/02/13/simple-sniffer-in-c.aspx) wrote a little sniffer in C# and decided I wanted to convert it to PowerShell. I ran into a little weirdness with the way that the bitwise operators worked in PowerShell. But before I talk about that let me describe the two ways to port your C# app to PowerShell.

- Just include the .NET code and call it
- Actually write it in PowerShell

```csharp
//1
Add-Type = @'some code here with MyClass class and MyMethod method'
//2
[MyClass]::MyMethod()
```

Below is a slightly modified version of my previous code. This runs as a synchronous

```csharp
Add-Type @'
using System;
using System.Net;
using System.Net.Sockets;
public static class Sniffer{
    public static string ToProtocolString(byte b){
        switch (b){
            case 1: return "ICMP";
            case 6: return "TCP";
            case 17: return "UDP";
            default: return "#" + b.ToString();
        }
    }
    public static void LogBuffer(byte[] buffer){
        Console.WriteLine(
            "{0} - {1} - {2}:{3} ===> {4}:{5} - {6} bytes"
            , DateTime.Now.ToString("yyyy,MM,dd,HH,mm,ss,fff")
            , ToProtocolString(buffer[9])
            , buffer[12] + "." + buffer[13] + "." + buffer[14] + "." + buffer[15], buffer[20] << 8 | buffer[21]
            , buffer[16] + "." + buffer[17] + "." + buffer[18] + "." + buffer[19], buffer[22] << 8 | buffer[23]
            , buffer[2] << 8 | buffer[3]);
    }
    public static void Sniff(string ip)
    {
        Socket s = new Socket(AddressFamily.InterNetwork, SocketType.Raw, ProtocolType.Unspecified);
        s.Bind(new IPEndPoint(IPAddress.Parse(ip), 0));
        s.IOControl(IOControlCode.ReceiveAll, BitConverter.GetBytes(1), null);//promiscuous
        byte[] buffer = new byte[s.ReceiveBufferSize];
        while(true){
            s.Receive(buffer);
            LogBuffer(buffer);
        }
    }
}
'@
 
#call the sniffer
[Sniffer]::Sniff("127.0.0.1")
```

#### Method 2

So Method 2 is also ‘fairly’ straight forward. I ran into some issues with the bitwise operators that caused me enough pain to warrant mentioning it here. The basic problem was that .NET automatically would convert the values to int when it read them out of the byte and PowerShell did not. So all I had to do is cast the byte values to int prior to performing the bitwise operations and all was well. =) The only thing that was a bummer about this is I had less knowledge about bitwise operation than I would like and I couldn’t visualize the problem. so I had to write a third sniffer that simply output the different value portions of the bitwise operation to see which piece was breaking. In any case, here’s the PowerShell version:

```powershell
#Resolves Protocol Numbers to Strings
function ToProtocolString($p){
    switch ($p)
    { 1 {"ICMP"};6 {"TCP"};17 {"UDP"};default {"#$p"};}
}
 
#Lobs Buffer data we care about to the screen
function LogBuffer($b)
{
    "{0} - {1} - {2}:{3} ===> {4}:{5} - {6} bytes" -F
        ([System.DateTime]::Now.ToString("yyyy,MM,dd,HH,mm,ss,fff")),
        (ToProtocolString($b[9])),
        ([string]$b[12] + "." + [string]$b[13] + "." + [string]$b[14] + "." + [string]$b[15]),
        ([string] ([int]$b[20] -shl 8 -bor [int]$b[21])),
        ([string]$b[16] + "." + [string]$b[17] + "." + [string]$b[18] + "." + [string]$b[19]),
        ([string] ([int]$b[22] -shl 8 -bor [int]$b[23])),
        ([string] ([int]$b[2] -shl 8 -bor [int]$b[3]))
}
 
#Sniff traffic on an IP address on your machine
#create a socket, set it up, bind to it, switch to promiscious mode, loop, receive data, echo data
function Sniff($ipaddress)
{
    $s = new-object system.net.sockets.socket(
        [Net.Sockets.AddressFamily]::InterNetwork,
        [Net.Sockets.SocketType]::Raw,
        [Net.Sockets.ProtocolType]::Unspecified)
    $s.bind((new-object system.net.ipendpoint(
        [net.ipaddress]$ipaddress,0)))
    $null = $s.iocontrol(
        [Net.Sockets.IOControlCode]::ReceiveAll,
        [BitConverter]::GetBytes(1),$null)
    $buffer = new-object byte[] $s.ReceiveBufferSize
    while($True){
        $null = $s.Receive($buffer)
        LogBuffer($buffer)
    }
}
 
#call the sniffer
Sniff("127.0.0.1")
```

#### Conclusions, or something like them...

Both scripts ran for me with no noticeable differences. Piping the output to a file if they both were started at the same time yielded the same information. There was no appreciable difference visible in Task Manager when I was utilizing the two scripts simultaneously. Interestingly the PowerShell version appears to use more CPU although I couldn’t tell without querying the win32_process for the details. I guess PowerShell must be re-initializing some values or variables or something on each pass. Below is maybe 30 minutes worth of data. Every time I ran the packages simultaneously I got similar results.

```powershell
PS C:\temp> gwmi win32_process | where {$_.CommandLine -like "*BasicSniffer*"} | select CommandLine, PeakPageFileUsage, ReadTransferCount, WriteTransferCount, KernelModeTime, UserModeTime | format-table -auto

CommandLine                          PeakPageFileUsage ReadTransferCount WriteTransferCount KernelModeTime UserModeTime
-----------                          ----------------- ----------------- ------------------ -------------- ------------
powershell  .\BasicSniffer.ps1                   68308           1361050               6205        2496016     28860185
powershell  .\BasicSnifferDotNet.ps1             62796           1377036               8129        2652017      3588023
```

Unfortunately the inability to figure out the bitwise operator thing distracted me quite a bit. I will also point out that you could just use a call like `((ushort)IPAddress.NetworkToHostOrder(BitConverter.ToInt16(buffer, 20))).ToString()` to get the values I am performing bitwise operations on. But I didn’t want to do it that way. Actually I didn’t really care to *not* do it that way but the fact that I couldn’t figure out how to directly move the code over using the same methodology as the C# code made me really want to figure it out. I hadn’t even intended to *really* blog anything but a translation of the code, but the PITA-factor related to not knowing I needed to cast that byte value to int caused me enough issues I wanted to document it.

For anyone who is not aware of what I am doing with the fun lines involving << and –shl and so on, I will summarize a bit.

- There are some values stored in the IP and TCP header (in this case, Size in IP header and Source/Destination Ports in the TCP header) that are multi-byte.

- Multi-byte values can go up to 64k , also known as 65535 in normal human terms (aaka, base 10).

- What this means is that we need to read the values out of two different byte fields. If we were reading the data bit by bit, we could simply add up the data where we need to, but we are getting the buffer back from Winsock as a byte array so I’m just dealing with it as a byte array.

- So the number we are looking to get to convert to an int is a 16 digit binary value, like 000001001000111, but what we will get are 2 byte values that look like this 0000010 01000111.

- In order to get that number, we have to do a bitwise shift and inclusive or</li>

- First we pick the first ‘byte’ which is (in this example) 0000010. 0000010 is actually equal to 2 in decimal, but *REALLY* this number represents 000001000000000 (512) because it’s part of a multi byte value. we just can’t see the rest of the value because it’s in the next byte. This is ok though because we’ll kind of save this value and add the value to the next part by converting it to what it really is by using shift. Since we are shifting it over by one byte, we have to shift it 8 bits. The syntax in the code above shows examples of this. C#: `b1 << 8 or PS: $b1 -shl 8` converts the 0000010 to 000001000000000. Basically we just tacked on 8 zeros. So now we have a part of a fixed number to work with, but we need the rest of the actual value.

- We now need to get the next portion of the value. In our example number here it is 01000111 (71). Really, we already have it. All you have to do is refer to the 2nd byte and you have it’s value as there is no transformation needed. So now we have it, what do we do with it?

- To combine these numbers we use bitwise inclusive or. C#: `b1|b2 or PS: $b1 -bor $b2`. Basically this is like ‘If either of the values when we check are 1’s, make the merged value a 1’. So we put 000001000000000 on top of 01000111 and move all the 1’s down. So you end up with 1001000111 which is 583.

- You can also just add the integer values of the shifted first byte shifted and the 2nd byte, but it’s easier just to Xor them together I think since you have to shift one anyway and are thus already doing bitwise operators.

- So the full syntax is something like this: C#: `b1<<8|b2 PS: $b1 -shl 8 -bor $b2`. But in the case of my code, I am not actually creating byte values, but I am getting them out of a byte array. because of this if you don’t cast the values to int values, the shifting doesn’t work right. so for PS you would do `[int]$bArray[1] -shl 8 –bor [int]$bArray[1]`

- Hopefully that helps someone who is/was on the cusp of understanding how some of the bitwise/binary stuff works. If you don’t know anything about binary or bitwise operators this may be a little much.

I wasn’t able to find a really simple way to do the eventing with PowerShell because the ‘onreceive’ method of the socket is something you have to call, not an event. I just went with this synchronous method that binds to a single local IP. I’m sure there is some way to do a console.read in PowerShell that will keep piping data out to the screen but I didn’t feel like messing with it too too much by the time I was done with the bitwise drama. Embedding the asynchronous C# code worked just fine so if I absolutely wanted to use the async method and I absolutely wanted to use it in PowerShell , I could just embed the C# code. Maybe one day I’ll revisit that whole issue. PowerShell is using .NET libraries to interface with Winsock anyway, so I have a hard time justifying spending too much time puzzling out how to do the harder stuff in PowerShell when I could just embed C# code just as easily. I would like to know exactly why there is so much more CPU time taken up by PowerShell, but that’ll have to wait for another day.

#### Sources

Some PowerShell Resources:

http://blogs.technet.com/b/heyscriptingguy/archive/2010/02/09/hey-scripting-guy-february-9-2010.aspx

http://blogs.technet.com/b/josebda/archive/2010/04/08/powershell-v2-get-date-vs-system-datetime-now-what-s-the-difference.aspx

http://learningpcs.blogspot.com/2012/06/powershell-v2-playing-with-bor-and-band.html

http://stackoverflow.com/questions/11329664/asynchronous-powershell-script

http://www.drdobbs.com/windows/building-gui-applications-in-powershell/240049898?pgno=2

http://blogs.technet.com/b/heyscriptingguy/archive/2011/06/16/use-asynchronous-event-handling-in-powershell.aspx

http://www.hofferle.com/embed-a-c-program-in-a-powershell-script/

http://stackoverflow.com/questions/5260125/whats-the-better-cleaner-way-to-ignore-output-in-powershell

Endianness:

http://en.wikipedia.org/wiki/Endianness

My Previous Post:

http://www.drowningintechnicaldebt.com/RoyAshbrook/archive/2013/02/13/simple-sniffer-in-c.aspx

