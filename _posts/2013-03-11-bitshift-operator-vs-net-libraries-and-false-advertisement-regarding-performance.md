---
layout: post
title: Bitshift operator vs .Net Libraries (and False Advertisement regarding Performance)
---

Recently I have written a few blog entries about basic sniffer code. One of the most basic things that a sniffer has to do is gather packets and break out some data. My sniffers were all very simple, really only dealing with analyzing the packet headers themselves for IP and TCP headers. Within the headers there were a few values that I was concerned with that were multi-byte values. I have already written about this in my last blog post, but I thought I would post about something I noticed that really impacts the crazy problem of early optimization.

The three fields that I cared about that had multi-byte values were Size, Source Port, and Destination Port. For all of these values, given two bytes of data I found 2 ways to gather the data and present it. There are several conversions that must take place. The data is stored in a byte array, but it’s not in the correct order for most windows systems. The Endianness on windows systems (in general) is Little-Endian. But the order for network communication (in general) is Big-Endian. You can search on google for more info on Endianness and Endian related drama as I don’t really want to go into that here.

What I do want to go into is my false-positive optimization. I found that the following two methods can be used to get the data out of the Big-Endian value and convert it to a string:

```csharp
((ushort)(buffer[0] << 8 | buffer[1])).ToString();
((ushort)IPAddress.NetworkToHostOrder(BitConverter.ToInt16(buffer, 0))).ToString();
```

Why does it need to be a string? Because ultimately I am presenting this data to the screen for consumption. The results are basically the same though even if you just get the raw data and don’t convert it to short or string.

In any case, basically the two methods are:

- shift the first bytes bits left by one byte and bitor it with the second byte to get a total
- use BitConverter.ToInt16 to get the int value from the 2 bytes, but it will be in incorrect byte order so you can fix it with the IPAddress.NetworkToHostOrder call.

There are most likely other permutations of this conversion, but these are kind of my ‘meta’ two scenarios to test and what is in the code above. I went with various different implementations of one or the other depending on my mood while I was fiddling with the sniffers, but once I was done with that, I wanted to pick one at least in my own mind as a ‘standard’ for me to use. So I decided to write up a quick test. The simplest way to test something seemed to be to just run it repeatedly and remove as many extra variables as I could. So put each method in it’s own action, wrap it with a stop watch, then run that action X times and record some total for which was fastest. In this case, I just ran them both in sequence and checked to see which ran fastest and created a ‘win’ counter for one of them to check against. I figured bitwise operations would be fastest, so I made that my first method, Method A. I then decided I wanted to run a large sample repeatedly against random data so I wrapped the loop call in another loop call that would produce a random value between 0 and 64k (the max value for 2 bytes) and then check it. Here is that code:

{% gist d5f0e2f3976e6a720c1efb4e87ccd15a %}

This produced:

```
Wins - A:8834 B:1166 - AWin% = 88.34%
Wins - A:8968 B:1032 - AWin% = 89.68%
Wins - A:8834 B:1166 - AWin% = 88.34%
```

And lots of other numbers like that. Seems clear that Method A is the winner!!!

But wait! What happens if instead of running each random value for 1000 loops, we just run it once. I mean, that’s not *really* a true stress of each method, but that’s what we are going to be doing anyway (one packet, gets one cycle for it’s buffer). Hmm…. Let’s change ‘tc’ in the above code to 1… or maybe even 10…

```
int tc = 1;
Wins - A:2893 B:7107 - AWin% = 28.93%
Wins - A:3507 B:6493 - AWin% = 35.07%
Wins - A:3458 B:6542 - AWin% = 34.58%

int tc = 10;
Wins - A:3343 B:6657 - AWin% = 33.43%
Wins - A:3449 B:6551 - AWin% = 34.49%
Wins - A:3449 B:6551 - AWin% = 34.49%
```

Interesting. What gives? Well, I don’t know exactly. Here is the IL that is produced within LinqPad:

```
<>c__DisplayClass4.<Main>b__1:
IL_0000:  nop         
IL_0001:  ldarg.0     
IL_0002:  ldfld       UserQuery+<>c__DisplayClass4.buffer
IL_0007:  ldc.i4.0    
IL_0008:  ldelem.u1   
IL_0009:  ldc.i4.8    
IL_000A:  shl         
IL_000B:  ldarg.0     
IL_000C:  ldfld       UserQuery+<>c__DisplayClass4.buffer
IL_0011:  ldc.i4.1    
IL_0012:  ldelem.u1   
IL_0013:  or          
IL_0014:  conv.u2     
IL_0015:  stloc.1     // CS$0$0000
IL_0016:  ldloca.s    01 // CS$0$0000
IL_0018:  call        System.UInt16.ToString
IL_001D:  stloc.0     // v
IL_001E:  ret         

<>c__DisplayClass4.<Main>b__2:
IL_0000:  nop         
IL_0001:  ldarg.0     
IL_0002:  ldfld       UserQuery+<>c__DisplayClass4.buffer
IL_0007:  ldc.i4.0    
IL_0008:  call        System.BitConverter.ToInt16
IL_000D:  call        System.Net.IPAddress.NetworkToHostOrder
IL_0012:  conv.u2     
IL_0013:  stloc.1     // CS$0$0000
IL_0014:  ldloca.s    01 // CS$0$0000
IL_0016:  call        System.UInt16.ToString
IL_001B:  stloc.0     // v
IL_001C:  ret         
```

The calls out to the .NET libraries definitely make the code shorter. and we have to make two calls to the buffer for ours. I assume that the .NET team’s stuff is just faster than my humble shifting. I will also say that in my original code, I just converted the shift formula to a string without going to ushort first and that was slower every time. So it pays to do a little testing, but not too much. I could have just stuck with the BitConverter and the NetworkToHostOrder conversions. In many cases I didn’t even really register a tick difference unless I went with at least 10 cycles, that’s why I ever started at 1000 per number because it gave me a seemingly significant number to compare for the two operations. But even though the shifting is faster if it’s run a lot (and it’s much faster if you run tons and tons more cycles) it’s not relevant because you only will ever in practice (for my purposes anyway) run that cycle a single time.

So I guess as is normally the case, just use the libraries that MS gives you for tasks like this. If they gave you a method for it, it’s probably faster than just doing it yourself for normal use. =P

Reference:
- http://learningpcs.blogspot.com/2012/06/powershell-v2-playing-with-bor-and-band.html
- http://blogs.technet.com/b/josebda/archive/2010/04/08/powershell-v2-get-date-vs-system-datetime-now-what-s-the-difference.aspx
- Too many StackOverflow articles to count on things like this. =P