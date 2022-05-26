---
layout: post
title: Simple Sniffer in C#
---

Recently I had a need to audit network traffic on some servers. Believing we had appropriate monitoring in place I placed a request to the right folks to retrieve this information. During that process I decided to do something that’s been on my list of ‘programs to write’ for a while: write a sniffer.

My requirements:

- I only want Protocol Type, Source/Destination IPs and Ports (No Packet Data)
- Be promiscuous (meaning, get all of the data that we can see, even if it’s not for me)
- IPv4 only

You could argue that this is more of a packet header logger than sniffer, but the logic is there if you want to parse the rest of the packet. Using the references  below you can find classes for the full IP and TCP headers as well as other info. Using that you could cache all of the packet data etc. I didn’t really want to build something to view cached data (like mjsniffer below), I just wanted the basics about the packets that are in flight. Anyway, the code is below. You can actually copy/paste this almost into LINQpad and run it there if you want. You have to convert the usings and a couple of other details but it works fine.

I have saved this project on codeplex with the project name SimpleSniffer, but included below is all of the code. This is “slightly” different than what’s on codeplex. The main difference is in this code I actually process the bytes as they come in and directly Console.Write the value from the buffer. In the code on codeplex I am shooting a subset of the resulting packet data (the last 15 bytes of the 24 I am grabbing) to a method and letting it write it. I thought this was a little bit smaller so I am leaving this version here.

{% gist 9b3d0508605b98e38439e5dab1d149e8 %}
