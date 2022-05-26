---
layout: post
title: recursion and file enumeration
---

Very simple app that just shows how to traverse files and folders. Note that you can also utilize the FileSystemInfo class, however if you do that you have to write your own file filter checking etc. Not that the default is super robust, but it does end up being less code. Just for a change, I short cut the class references with using statements up top =P

I didn't just do this randomly, I was actually looking at using the c++ glob app shown here in my c# project and maybe just cycling through a glob instead of traversing the tree, but after testing them on an svn repository with a few thousand files, the difference was so small that i couldn't justify adding all that extra code and including a c++ dll etc. Not to mention, i would have to *write* the dll as the only thing included was an exe. That wouldn't be too bad, but there is only so much time in the world and this was so much quicker. =P

it's still probably fastest to just do a shell out and dir /s /b *.whatever and parse the text, but whatever.

{% gist b6141866934a27e437fb691d8de9c23d %}

Here's a sample of what you could do for the FileSystemInfo class. Note, when i wrote this i was short cutting this class with a using statement tool. Also the FileAttributes class with the fa var.

{% gist 4e3e907cd79923aa674c9cbfc1f7a015 %}
