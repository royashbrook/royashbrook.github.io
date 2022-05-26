---
layout: post
title: How Heroku made me sad today =(
---

<p>Yes, <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a> made me sad today. <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a>!? What?! I can hear you saying it! =P Fortunately I was just coming down off of a fun trip after refreshing myself on the <a title="Online Academy for Coders!" href="http://www.codecademy.com/" target="_blank">Codeacademy</a> ruby course. Really nothing there if you already use ruby quite a bit, but I haven’t written any ruby in a couple years and it’s always nice to remember how pleasant that language is to work in. That ruby high is all that kept me plugging away at putting <a title="Dex! The Super Downloader for EXEs. Ok, well it's not super, but it downloads them!" href="http://www.drowningintechnicaldebt.com/RoyAshbrook/archive/2013/04/04/appharbor-and-dealing-with-annoying-firewall-rules.-p.aspx">dex’s</a> evil twin ruby brother ‘fwad’ out on <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a>. But first… how did <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a> make me sad? Well FIRST-first, a little backstory.</p>  <p>Lately I have been doing a lot of research into cloud providers and looking at various hosted solutions for all sorts of things. So fiddling with these two services over the last couple of days gave me some time to both play with ruby and play with <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a> and <a title="App Harbor; Find a port in the storm for your .NET apps!" href="https://appharbor.com/" target="_blank">AppHarbor</a>, two pretty popular application hosting/cloud services. I originally had the idea used for dex in ruby. I wrote a local client in ruby that did everything except stream the data in a response. I say “everything” but it should be in quotes the first time as well because I just wrote something simple that would download a file and write it to disk. On a whim, I also thought “hey, why not have it gzip the data as well?” So that’s what I wrote. In fact, here is the local client in all of it’s glory:</p>  <pre class="csharpcode">require <span class="str">"open-uri"</span>
require <span class="str">"zlib"</span>
url = <span class="str">'http://somesite.with/an/exe/todownload/somefile.exe'</span>
gzf =  <span class="str">'c:\temp\somefile.exe.gz'</span>
open(url) { |rf| #open remote file
  File.open(gzf, <span class="str">'w+b'</span>) { |lf| #open local file/stream
    Zlib::GzipWriter.open(lf) { |gz| #open gzipper on local file/stream
      gz.write(rf.read) #write the compressed data
    }
  }
}</pre>
<style type="text/css"><![CDATA[
.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }]]></style>

<p>Not too glorious, eh? Well it was meant to be really really simple. I think I spent like 60 minutes getting ruby installed and writing this little dealio. It worked fine and thus began my campaign to somehow promote it, as a web app, to <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a>. I already had an account from when they were in beta, how hard could it be? Well, apparently for someone who works primarily on windows, without git, and without normal ruby DEV tools installed, it’s a bit of a pain. Now, I will say that it isn’t really <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a>’s fault that I was somewhat ignorant of what I wanted at the time. But in my defense the first thing I did was try and get a simple hello world site setup as described in their Getting Started with Ruby on <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a> tutorial here: <a href="https://devcenter.heroku.com/articles/ruby">https://devcenter.heroku.com/articles/ruby</a>. Now, I’m not going to rehash my entire silly experience. But I will say that I tried to follow the directions and I still ended up having to piecemeal my solution together to get the initial Sinatra application to work. The main culprit in my whole story was this snip from that page:</p>

<blockquote>
  <h4><a href="https://devcenter.heroku.com/articles/ruby#declare-process-types-with-procfile">Declare process types with Procfile</a></h4>

  <p>Use a <a href="https://devcenter.heroku.com/articles/procfile">Procfile</a>, a text file in the root directory of your application, to explicitly declare what command should be executed to start a web <a href="https://devcenter.heroku.com/articles/dynos">dyno</a>. In this case, you simply need to execute the <code>web.rb</code> using Ruby.</p>

  <p>Here’s a <code>Procfile</code> for the sample app we’ve been working on:</p>

  <pre>web: bundle exec ruby web.rb -p $PORT</pre>

  <p>If you’re instead deploying a straight Rack app, here’s a <code>Procfile</code> that can execute your <code>config.ru</code>:</p>

  <pre>web: bundle exec rackup config.ru -p $PORT</pre>
</blockquote>

<p>Somehow I missed the top one and put the second one in. So my app kept crashing and I had to become more familiar with debugging stupid self inflicted errors on <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a> than I wanted to. Anyway, once I figured all that out and all the other silly stuff, I ended up uploading a straight rack application. I found this link (<a href="http://rubylearning.com/blog/a-quick-introduction-to-rack/">http://rubylearning.com/blog/a-quick-introduction-to-rack/</a>) to be very helpful. So this process took me on and off almost a day to figure out and get the code written/pushed/etc. I went with rack because by the time I was done with this I had already long since rewritten the app in C# (after downloading and installing the vs. web express as I didn’t have MS web DEV tools installed, only Win/Console App tools) and deployed it to <a title="App Harbor; Find a port in the storm for your .NET apps!" href="https://appharbor.com/" target="_blank">AppHarbor</a>. that includes setting up <a href="http://www.codeplex.com/" target="_blank">CodePlex</a>, etc., etc. Since I hadn’t gotten my hello world app up, I hadn’t finished the ruby version, so I wrote the C# version first really. I say that only because I can’t say I ‘ported’ the ruby code. So back to why I went with rack, it was just because I could write all the code in one page and keep it really simple. </p>

<p>So what mad me sad is it was so much fun to write my little ruby script and then it was so much more of a pain to get it up to <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a> than I thought it would be. I realize that it was not <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a>’s fault really, but maybe they should have an even MORE handholding tutorial =P I felt like I had to click on 100 different links to go get a tutorial on this thing or that thing. really I would have been happy to just have them generate the app in their tutorial and then let me do a git from some prebuilt repo and I could edit it from there. I’m sure not everyone wants that, but it really would have helped me out today. </p>

<p> </p>

<p>So here, finally, is FWAD( firewall avoidance downloader! =D )! I haven’t decided if I like the pronunciation ‘EF’-‘WAD’ or ‘fWAD’ better yet.</p>

<p>Anyway, the entire contents of the app is a Gemfile with:</p>

```ruby
source http://rubygems.org
rack
```

<p>And a config.ru with:</p>

<pre class="csharpcode">require <span class="str">'open-uri'</span>
require <span class="str">'zlib'</span>

run lambda {|s|
    
    headers = {}
    response_body = <span class="str">''</span>
    path = s[<span class="str">'PATH_INFO'</span>] 
    path += <span class="str">'?'</span> + s[<span class="str">'QUERY_STRING'</span>] <span class="kwrd">if</span> s[<span class="str">'QUERY_STRING'</span>] != <span class="str">""</span>
    key = <span class="str">'YaTl7a4akBMefeCZ'</span>
    op = path[1,16]
    
    begin
        <span class="kwrd">if</span> (op == key || op == key.reverse)
        
            url = path[18,path.length-18] + <span class="str">'.exe'</span>
            response_body = open(url,<span class="str">'rb'</span>){|i|i.read}
            
            <span class="kwrd">if</span> (op == key)
                ct = <span class="str">'application/octet-stream'</span>
                cd = <span class="str">'attachment; filename=renameme.txt'</span>
            <span class="kwrd">else</span>
                ct = <span class="str">'application/x-gzip'</span>
                cd = <span class="str">'attachment; filename=file.gz'</span>
                bn = File.basename(url)
                bn = bn.include?(<span class="str">'?'</span>) ? <span class="str">'renameme.txt'</span> : bn
                output = StringIO.<span class="kwrd">new</span>
                gz = Zlib::GzipWriter.<span class="kwrd">new</span>(output)
                gz.orig_name = bn
                gz.write(response_body)
                gz.close
                response_body = output.<span class="kwrd">string</span>
            end
            
            headers[<span class="str">"Content-Type"</span>] = ct
            headers[<span class="str">"Content-Disposition"</span>] = cd
            
        <span class="kwrd">else</span>
            response_body = <span class="str">'no dude.... just... no.'</span>
        end
    rescue
        response_body = <span class="str">'no dude.... just... no!'</span>
    end

    headers[<span class="str">'Content-Type'</span>] ||= <span class="str">'text/plain'</span>
    headers[<span class="str">'Content-Length'</span>] = response_body.length.to_s

    [200, headers, [response_body]] #we lie and say we're always ok =D

}</pre>

<p> </p>

<p>Note: You do still need a procfile and a Gemfile.lock to push up to <a title="Heroku Happiness!! Forget your servers. Focus on your code!" href="https://www.heroku.com/" target="_blank">Heroku</a> so it will work. It works similar to dex however, since I could intercept the call before it went anywhere, I went ahead and ghetto handled my own routes. I used the same key as a dex for uncompressed download to txt and I set the reverse of that key to be the route for compression. I also kinda went cheap on embedding the filename in the zip. If someone sends a filename that is in the querystring, I piecemeal that in earlier in the code, but File.basename in ruby will see a full filename as something like “filedownloader.php?myfile.exe”. So I just avoid that whole mess and if there is a ‘?’ (meaning a querystring) I just send gzipped renameme.txt.</p>

<p>Here’s a sample link to download <a href="http://linqpad.com/" target="_blank">LINQpad</a> gzipped- <a title="http://fwad.herokuapp.com/ZCefeMBka4a7lTaY/http://www.linqpad.net/GetFile.aspx?LINQPad" href="http://fwad.herokuapp.com/ZCefeMBka4a7lTaY/http://www.linqpad.net/GetFile.aspx?LINQPad">http://fwad.herokuapp.com/ZCefeMBka4a7lTaY/http://www.linqpad.net/GetFile.aspx?LINQPad</a></p>

<p>Here’s a sample link to some weird googlecode uncompressed- <a href="http://fwad.herokuapp.com/YaTl7a4akBMefeCZ/https://lightpack.googlecode.com/files/PrismatikSetup_5.9.4">http://fwad.herokuapp.com/YaTl7a4akBMefeCZ/https://lightpack.googlecode.com/files/PrismatikSetup_5.9.4</a></p>

<p>Here’s a link to the troublemaker exe that started all this - <a title="http://fwad.herokuapp.com/YaTl7a4akBMefeCZ/http://inedo.com/files/buildmaster/sql/3.5.8" href="http://fwad.herokuapp.com/YaTl7a4akBMefeCZ/http://inedo.com/files/buildmaster/sql/3.5.8">http://fwad.herokuapp.com/YaTl7a4akBMefeCZ/http://inedo.com/files/buildmaster/sql/3.5.8</a></p>

<p>Pretty simple, you just call the service with the appropriate route and some path to an exe without the exe on the end and it will send you a file.</p>

<p>Moral of the story: know what you are doing before you start doing it. don’t expect magic tutorials to save you. =)</p>
