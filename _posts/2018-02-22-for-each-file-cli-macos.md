---
layout: post
title: For each file - CLI - MacOS
published: true
date: '2018-02-22'
---

I had a need to cycle through some files to convert the encoding today. Had to lookup how to do this from the cli/terminal on a mac.

``` sh
for f in ./_posts/2007*.md;
do
iconv -sc -f UTF-16LE -t UTF-8 $f > $f.markdown;
done
```

This is just (basically) a one liner to look at all my posts from 2007 and convert them from utf-16le encoding to utf-8 and rename them with markdown after. This worked ok for me, but sadly I still had to go back and manually fix a bunch of the posts.

I'm still not sure exactly what caused this to happen, but I suspect it is because I have been machine hopping while I convert the old blog.

After this, I also needed to remove the old .md files and remove .markdown from the files I just created.  To get rid of the old files, I just used rm, _as you do_. To rename the files I actually went and got rename and used that like this:

``` sh
brew install rename
rename -nvs md.markdown md *.md.markdown
```

The 'n' let's you get a preview of what will change. You just remove it to make it happen.
