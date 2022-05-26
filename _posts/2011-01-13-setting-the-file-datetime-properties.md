---
layout: post
title: Setting the File DateTime properties
---

This is pretty much a no brainer for code. However I went and looked and everything I found was either overkill, or i wasn't certain of the internals so i had to say run it, look at the properties etc.

So I wrote a simple app to do it and put it on codeplex at:
http://setfiledate.codeplex.com/

This problem stemmed from the fact that I got this weird DVD player and I needed it to play some files from a USB stick in a loop. It loops fine, the problem was ordering. No clue how the ordering was. I thought it might be one of the dates so I tried setting them all manually, no dice. It's not the end of the world as I'll just make a looping DVD or a single media file which will loop, but I tried this first. =)

Here's the set code if you actually came to this post looking for that =P

```csharp
File.SetCreationTime(f, dt);
File.SetLastAccessTime(f, dt);
File.SetLastWriteTime(f, dt);
```

Where f is a string file path and dt is a DateTime value.