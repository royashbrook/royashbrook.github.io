---
layout: post
published: true
title: Getting data out of Powershell Jobs/Runspaces in realtime
date: '2021-04-19'
subtitle: '$YourFriend = [HttpListener] + Invoke-RestMethod'
---
Powershell gives you a variety of ways to do multithreaded or parallel processing. You can use jobs, runspaces, or foreach -parallel, or maybe come up with your own exotic way of starting processes and gathering the results back in if you need them.

But frequently while doing the work on these processes, you may need to troubleshoot some issue *inside* the process. And frquently during that process, if things don't go perfect, you may think 'man... i wish there was just a way to spit out some information on this job so i could make sure $x is definitely = to 3 or i could see the value of $y or whatever'. One way to do this is with a log file. You could just append data to a file and then maybe tail the file to watch. But wouldn't it be nice to just see it in a window so you could see the msgs come in in real time just like you would do with a single threaded setup where you write-host or whatever?

Well, here is a cheap and easy solution that uses a little web server.

{% gist 1bf9beda5e587d00c848e22af7fa83d0 %}

So this example is meant to be run in a single window all at once for illustration purposes, however the concept is simple. Basically it boils down to:

1. Run a web server that receives body data and echoes it to the screen
2. Have async processes make calls to that web server with the messages

That's it. And this is super easy to accomplish in powershell. The web server logic is like this:

```ps`
#create a new httplistener, this is the 'web server'
$http = [System.Net.HttpListener]::new() 
#define the prefixes to listen for including the port
$http.Prefixes.Add("http://localhost:8080/")
#start listening
$http.Start()
#basic status message
"started"
#loop while the web server is listening
while ($http.IsListening){
	#getcontext will block until it receives a request, so the loop sits here if there is no request
    $cx = $http.GetContext()
    #as soon as we get a request, just output the stream to the screen
    [System.IO.StreamReader]::new($cx.Request.InputStream).ReadToEnd()
    #close the output stream so the client will get a connection closure
    # otherwise the client will just sit there until it timesout eventually
    $cx.Response.OutputStream.Close()
    #add a way to kill the web server. if you put in /kill on url, it will break out of loop
    if($cx.Request.RawUrl -contains '/kill'){break;}
}
#if we're out of the loop, get rid of the listener
$http.Dispose()
```

So now that we have a web server we can really call it however we want. I have been using Invoke-RestMethod because there is no progress info and I really don't need a full web request. I am really just sending data anyway. But notice I am not dealing with post/get/whatever. In my case I don't care, i just listen for some connection and then handle the body.

So for me, I simply use `irm http://localhost:8080 -body "somevalue"` to send some value to the web server.

Now you can do some fancier things like maybe you want to have the web server show the client or show the time or something on the message. I just output it and I do that in the job personally as I'm really only interested in raw data inside of the job.

So you could run this web server independent of the job. But in my gist above I am running it in a single script. So to do that, I add a little loop at the top of the scriptblock that basically waits until it sees the web server. Then I start all of my jobs which will just sit and wait to do processing until they see the web server, and finally I start the web server. Once it is up, data starts flowing in from the jobs.

Now I'm not sure on the scaling for this as I've only tested it with about 10 jobs, but it seemed to work fine for that. And I imagine if you are just troubleshooting, this will do the trick and then you can remove it since you may not need to get that data out. I just kind of stumbled on this when I was playing around with some existing code and then I just wanted to make an example. But Honestly you can just spin up the web server in it's own window and then use the irm calls in any existing setup with minimal impact.

