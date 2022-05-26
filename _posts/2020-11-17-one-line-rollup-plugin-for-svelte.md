---
layout: post
published: true
title: one line rollup plugin for svelte
date: '2020-11-17'
subtitle: git log -1 > something...
---
I am a simple man. I have simple needs. Like I need to output the results of `git log -1` into a file with some formatting when I build. Not so much to ask. Here is a line to do that: `{generateBundle(){require('child_process').exec("git log -1 --pretty=format:%H%n%ae%n%ad > .\\public\\build\\commit.txt")}},`. Basically you just add this to your rollup.config.js in the plugins section. How about a little more explanation?

## um... wut?

I have a svelte project. I wanted to include a way to tell what git commit I was using when I deployed it around. [Here](https://www.hanselman.com/blog/adding-a-git-commit-hash-and-azure-devops-build-number-and-build-id-to-an-aspnet-website) is a great blog post on how to do this properly in the dotnet world. I am using devops to deploy, but my app is a svelte app and I simply have the pipeline set to deploy the public folder on every checkin to a vnext site. I wanted a similarly simple solution for adding some git hash to my build output.

## ok, so about the one line..

Yes, yes, yes. I know it's a _long_ line. =). But it is what it is. So if you aren't familiar with [svelte](https://svelte.dev/), it is a js framework and it uses [rollup](https://rollupjs.org/), by default, as it's bundler. You can use whatever you want, but I'm using rollup since that's what it came with. In rollup, you can add plugins to do different things. I couldn't find one that did what I wanted which was simply to add some way to see the git hash that the current build was deployed from. So I looked for how to write one. I came across [this](https://lihautan.com/12-line-rollup-plugin/) great article that totally gave me what I was looking for. It's even easier than I thought since you can just put the plugin inline. If you are using svelte and open up your rollup.config.js, you should see the top of the plugins area looking something like this:

```javascript
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
```

and you can just add it just below that last line like:

```javascript
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
        {generateBundle(){require('child_process').exec("git log -1 --pretty=format:%H%n%ae%n%ad > .\\public\\build\\commit.txt")}},
```

## remind me... what are we doing again?

I want to add some type of artifact or way to associate a git hash with the deployed code. So when the site is out there, I can look somewhere and see what git commit it is. I had no requirement on the format of the output. Like to put it in index.html, or another file, or whatever. So I am just looking for the easiest way. I knew I could get the info I wanted by just running `git log -1` because i just wanted the last commit. I just wasn't sure how to actually get that info in via code and do something with it. Enter [child_process](https://nodejs.org/api/child_process.html#child_process_child_process_exec_command_options_callback). This seemed to do what I needed. If you read into this, you can actually access stdios and originally I went that route. But ultimately I decided it was just easiest to simply dump the results of the command out to a file. By default, the working directory will be the current dir and I am always building from within this directory anyway so running the exact command I wanted and having it output the file in that command worked well. I didn't really want any messages, just the hash and the name/email and a date. But you could chang the command to whatever you want.

Also since `{generateBundle(){require('child_process').exec("git log -1 --pretty=format:%H%n%ae%n%ad > .\\public\\build\\commit.txt")}},` is a little bit hard to read, here is a prettied up and commented version.


```javascript
{
	name: 'gitlog1',
	generateBundle() {
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
},
```

I'm running on windows, and am just letting exec use the default shell, which is cmd. If you are on another operating system or want to use another shell you'll need to make adjustments. The documentation on child_process has plenty of information on how to do that. For me, I just did the quickest thing. I am calling the plugin "gitlog1" but really it could be anything. And you could also really run anything you want.

I'll mention here that you need to make sure you don't run something you don't trust or make sure you don't have input values that you may not trust in here as you could delete things etc. So YMMV, buyer beware, etc etc. I am just hard coding my values so it works fine.

>> Edit: I actually wrote most of this a week or two before I published this. After playing having this in place through a few releases over the last couple of weeks, I found that I frequently updated after the actual commit. So I would make changes, build build build (this would update the commit.txt) and then finally I would push to src. So then I ended up with a commit from just previous in the commit.txt. While this isn't ideal, none of this really is. Ideally I would just do this on my CI/CD pipeline. And I could, but for this very tiny project, showing the prior commit also works fine because I can just look at the next commit to see what was pushed. I also considered switching the command up to `git diff --compact-summary && git log -1` or just spitting out more information, but ultimately this works fine for me and the point was just to show how easy it is to just snap in some custom stuff for rollup if you need something like that like I did.

