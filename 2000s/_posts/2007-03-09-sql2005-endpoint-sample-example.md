---
layout: post
title: sql2005 endpoint sample/example
---

i went to look for a super simple way to setup a test of the endpoint feature in sql 2005. i couldn't find one simple enough, so i made this one up.

here's some sql code. just run all of this. there are comments in here for what's going on.

{% gist 3c17e3e9fda6382c1c96cd4a6734ab82 %}

and here is the code for a simple c# command line application to consume this service. you'll need a web reference to the localhost wsdl. you can name it something else, but i just left it as localhost. you will also need to reference system.web.services. you can make tons of modifications to this default to add functionality, but this should get you started.

{% gist ca0cd938210ca5fd9e62417343fc9c90 %}

enjoy!!!
