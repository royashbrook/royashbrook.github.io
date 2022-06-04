---
layout: post
published: true
title: Watching Files with Powershell
image: 'https://vignette.wikia.nocookie.net/vsbattles/images/e/e5/Uatu_the_Watcher.png'
date: '2018-07-24'
subtitle: 'Uatu, the powershell script'
---
I needed to watch a folder to see when files were created. I also decided to check for deleted files, below is the script. You can, of course, just look at the folder to see this in explorer. In this case, we had some files moving from one folder to another as part of a workflow, so this made it easy to just see when the files were coming in and getting removed and created.

Sometimes, you just need to watch things. With powershell this is pretty easy.

{% gist bf8419a82dbc6de5c4a7b160256d446b filewatcher.ps1 %}

![uatu](https://www.cheatsheet.com/wp-content/uploads/2016/09/The-Watcher-Marvel-Comics.jpg)
