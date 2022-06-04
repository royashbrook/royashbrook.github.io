---
layout: post
title: Simple RIAK and C# Example
---

Install It:

- Have a linux box somewhere. i spun up a new centos 7.0 droplet on digital ocean for 5 bucks and turned it off when i was done with it.
- http://docs.basho.com/riak/latest/ops/building/installing/rhel-centos/
    - Basically, I logged into my new machine and ran:
    - sudo yum install http://yum.basho.com/gpg/basho-release-5-1.noarch.rpm
    - sudo yum install riak
- If you want this to be available outside of your machine, you need to modify the ip address riak is bound to.
    - If you were logged in as root, go to the /etc/riak folder and edit the app.config
    - Modify the "http" area to have your public ip address as well as the 'PB' area up top.
- Run "riak start" to start up riak
- Check http://<your ip>:8098/riak/status should return some json
 

Use it:

- Open LINQpad (or whatever you want)
- NuGet search for riak
- Install CorrugatedIron
- Create a app.config file that is something like this:

```xml
<configuration> 
<configSections> 
    <section name="riakConfig" type="CorrugatedIron.Config.RiakClusterConfiguration, CorrugatedIron"/> 
</configSections> 
<riakConfig nodePollTime="5000" defaultRetryWaitTime="200" defaultRetryCount="3"> 
    <nodes> 
        <node name="mynodename" hostAddress="actualnodeaddress" pbcPort="8087" restPort="8098" poolSize="0" /> 
    </nodes> 
</riakConfig> 
</configuration>
```

*Note: I think this file can be named anything really, I actually called it riak.config and included a path down below.*

- try some c# code like this:

    ```csharp
    void Main() {
        //config file location
        var fl = @"path to my app.config";
        //name of the config section in the config file above
        var cs = "riakConfig";
        //name of our riak bucket to store things in
        var b = "mybucket";
        //connect to the cluster using our config file
        var clst = RiakCluster.FromConfig(cs,fl);
        //create a client
        var clnt = clst.CreateClient();
        //create an object to store
        var kvp = new KeyValuePair<string,string>("0","zero");
        //put it in a bucket in riak, key it, and drop the object in there
        var o = new RiakObject(b,kvp.Key, kvp);
        //put the object in riak
        clnt.Put(o);
        //go get the object using the same key as above
        var r = clnt.Get(b,kvp.Key);
        //dump the value we got back
        r.Value.GetObject<KeyValuePair<string,string>>().Dump();
    }
    ```