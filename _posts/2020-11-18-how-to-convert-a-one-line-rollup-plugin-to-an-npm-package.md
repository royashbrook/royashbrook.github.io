---
layout: post
published: true
title: how to convert a one line rollup plugin to an npm package
date: '2020-11-18'
subtitle: 'one line, two lines, three lines, four...'
---
I [just wrote](/2020-11-17-one-line-rollup-plugin-for-svelte) an article about creating a one line rollup plugin that would output the results of `git log -1` to a file on build. Super simple. Super basic. So easy, I just put it inline in the rollup.config.js file. But I wanted to see about converting it to a plugin module/script and also about making it into an npm package. So here's how I went about that.

## make a plugin file

So the first step was how to make this into a plugin file. Since I ultimately want this to be an npm package, I went with the way that you would expose things for that purpose. I am guessing you could also just do export defaults and import things up top in rollup.config.js, but I simply created a file called gitlog1.js that looked like this:

```javascript
module.exports = {generateBundle(){require('child_process').exec("git log -1 --pretty=format:%H%n%ae%n%ad > .\\public\\build\\commit.txt")}}
```

and then in my rollup.config.js I modified the plugins section to add what was being exported like this:

```javascript
//since i'm using svelte, the top of my plugins section looks like this
plugins: [
		svelte({
			// enable run-time checks when not in production
			dev: !production,
			// we'll extract any component CSS out into
			// a separate file - better for performance
			css: css => {
				css.write('public/build/bundle.css');
			}
		}),
  
// and i just added the require line directly below that for my file
plugins: [
		svelte({
			// enable run-time checks when not in production
			dev: !production,
			// we'll extract any component CSS out into
			// a separate file - better for performance
			css: css => {
				css.write('public/build/bundle.css');
			}
		}),
		require('./gitlog1'),
```

That simple. Ok, so now we have a plugin file, how to make this into something we can actually add to a file via npm?

## to the npm-mobile!!!

So to make this a published npm package you'll need some things.

- your package to publish in a folder
- preferably in github as well
- an npm login

That's really it. It's pretty simple. [Here](https://dev.to/therealdanvega/creating-your-first-npm-package-2ehf) is a great example on how to do this end to end. But I'll just cover some basic steps that I used. Basically the same with some commentary.

1. Make a new folder and run `npm init` in it. Fill out the name of your module, set the version etc. You can also just run `npm init -y` and then update package.json directly. My package.json looked like below for the initial release. Best to note the version and the package name as those are things that impact how you publish. You can't republish the same version and the name has to be unique.

```Javascript
//package.json
{
  "name": "gitlog1",
  "version": "0.0.1",
  "description": "adds a file with git log -1 results to build output",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Roy Ashbrook",
  "license": "ISC",
  "homepage": "https://github.com/royashbrook/gitlog1"
}
```

2. Make a new file index.js and paste your code in there. You can also use another file if you want but index.js is the default and is what I have set above. You can simply use my one line up top, but I updated the module a little bit so there was a console log regarding what it ran. This way I didn't have to just look at the file itself. My initial index.js is below. Note that I left the one-liner in there as it would work too. But I wanted to see that console log so i could be sure it ran during testing. I also changed the event hook to buildEnd vs generateBundle. You can check rollup for hook info. Running on buildEnd allowed it to run in order with some other plugins I already had so I updated it for the initial release.

```javascript
//index.js
module.exports = {
    name: 'gitlog1',
    buildEnd() {
        //git command to run
        const g = "git log -1 --pretty=format:%H%n%ae%n%ad";
        //where to pipe it to
        const o = ".\\public\\build\\commit.txt";
        //cmd to run g, pipe to o
        const c = `${g} > ${o}`
        //child process ref
        const { exec } = require('child_process');
        //exec c and show status
        exec(c,()=>console.log(`\n\ngitlog1 ran:\n\n\t${c}\n\n`))
    }
}
//module.exports = {generateBundle(){require('child_process').exec("git log -1 --pretty=format:%H%n%ae%n%ad > .\\public\\build\\commit.txt")}}
```

3. If you haven't published a package before, you'll need a login with npm. You can do `npm adduser` and then afterwards you'll need to confirm your email before you can actually publish anything. I was confirming on my iphone and found that the email link they sent me required me to be logged in on safari in order to verify. So YMMV. If you want to do what I did, I did `npm adduser`, followed the steps there, then I logged into the website on my phone on safari, and went and clicked the verify email in mail which opened the site in safari and I could click a button to confirm. If you don't finish doing the confirmation, you'll get an error when you do the next step like I did and have to try and figure out why you are getting it as the message doesn't really tell you anything other than 403.

4. `npm publish` - yep, that's it. Once you have added the user and you have the package, you can just publish it! Now how to use it?

5. Equally simple. For me, I just switched back to my previous project, ran `npm i gitlog1` then I updated my rollup.config.js as follows:

```javascript
//rollup.config.js

// update from this...
plugins: [
		svelte({
			// enable run-time checks when not in production
			dev: !production,
			// we'll extract any component CSS out into
			// a separate file - better for performance
			css: css => {
				css.write('public/build/bundle.css');
			}
		}),
		require('./gitlog1'),
  
// to this...
plugins: [
		svelte({
			// enable run-time checks when not in production
			dev: !production,
			// we'll extract any component CSS out into
			// a separate file - better for performance
			css: css => {
				css.write('public/build/bundle.css');
			}
		}),
		require('gitlog1'),
```

Pretty simple. Just remove to the reference to the file for require and instead use the package name. I then removed the file and that was it. =)

## Final thoughts

This was my first experience publishing a package to npm. I found it relatively pain free, but really because it was easy enough to find some helpful articles. Other than the email confirmation thing, this only took me like an hour maybe? I hope this maybe makes the experience similarly easy for someone else.

This package is how available [here](https://www.npmjs.com/package/gitlog1) if you like. Github link is on there as well.

