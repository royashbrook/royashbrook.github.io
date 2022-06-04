---
layout: post
published: true
title: Switching from JSZip to unzipit
subtitle: From easy to easier...
date: '2020-09-16'
---
This is about how I switched from using [JSZip](https://stuk.github.io/jszip/) to [unzipit](https://github.com/greggman/unzipit) in a javascript/spa application. I'll show the before and after code that I use for decompressing a payload from a URL.

## First a little context

I currently have a dashboard application that pulls some zipped data down, unzips it, and uses the json payload. There are reasons(tm) that I do this, but I was using JSZip to decompress the payload after a fetch, and then do stuff with it. The issue was the JSZip library was kind of large and in charge for me and [tree shaking](https://developer.mozilla.org/en-US/docs/Glossary/Tree_shaking) did not really seem to reduce the size even though I was only using the unzip portion. There may some wizardry to getting that to work, but my thoughts have been for a bit that I'd rather just find something that unzips only as that's all I need. My thinking was also, from writing some compression/decompression code many years ago that this should not be so big. The payload I was getting by downloading the min file from [cdn](https://cdnjs.cloudflare.com/ajax/libs/jszip/3.5.0/jszip.min.js) was 27.5k compressed and 95.8k as a resource size. Adding this via npm and hoping tree shaking would do it's thing didn't seem to reduce the size. I'm using rollup and this is going into a svelte project. I didn't try just importing the file and hoping tree shaking would happen, but I just kept occassionally looking for another library. There were some promising ones but I didn't find one that I liked. Until i stumbled across [unzipit](https://github.com/greggman/unzipit). In this case I just added the bundle and it only added ~8k to my total bundle size and i dropped the cdn for jszip. Their site says it (unzipit) is faster/better/etc and the goal seems to be exactly what I was looking for: a small unzip only javascript library.

## From this...

{% gist 4ab180673a9d50e734bae481e176d8c4 JSZip.js %}

## To this...

{% gist 4ab180673a9d50e734bae481e176d8c4 unzipit.js %}

## Conclusion

Pretty simple. I like it so far and it's working great in production with no issues. Seems faster although this operation is pretty small so I didn't go into any benchmarking for it. Definitely smaller.