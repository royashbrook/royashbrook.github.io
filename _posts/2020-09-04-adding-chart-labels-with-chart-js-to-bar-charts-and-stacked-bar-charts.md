---
layout: post
published: true
title: Adding Chart Labels with Chart JS to Bar Charts and Stacked Bar Charts
subtitle: A real life example
date: '2020-09-04'
---
I'll preface this by saying that you probably should just use the the official data label plugin from chartjs. You can find that [here](https://github.com/chartjs/chartjs-plugin-datalabels). I didn't want to add another dependency for charting, however, so I decided to just roll my own setup. Here are two ways I did this.

I will also add a caveat that I am not a chartjs expert. I do use it for my charting, but mostly that is because it handles combo charts easier than some of the other ones I tried. I don't have to touch these charts too much and I don't mind refreshing my memory every time I go to make updates.

## Plugin

Previously, I had a bar chart that showed some value for every day of the month and I needed to add a vertical line that showed where the 'Sunday's were to make it easier for people to see the week breaks. Probably other ways to do this visually, but the request from the users was just to add a verical dashed line with 'Sun' up top. After some research, I settled on doing this with a plugin. So, I first tackled this approach that same way. Below is some annotated code. You can take all the comments out and that's what is running right now for regular bar charts. There are various events to put this on. I am using afterRender because it doesn't 'run' as frequently. But this does mess with your hover and other animations. The easiest way to avoid this issue is to add `events: []` to your options. This basically skips any events like hover and other items. Now you can leave the events and then go with afterDatasetsDraw, but in my experience that seems to fire a bunch of times. I'm not sure why. In either case, if you don't want to remove your events and hover, just change afterRender to afterDatasetsDraw. You can see more about the event hooks [here](https://blog.larapulse.com/javascript/creating-chart-js-plugins). I'm not sure if I'm missing things or something, but I couldnt' find the same information from that blog on the documentation site itself.

### Regular Bar Charts

In my case, most of my charts had something similar to this. 

{% gist c19b5ed165ff1a93791cc6ddc7157234 BarChartLabels.js %}

### Stacked Bar Charts

I then ran into a situation where I needed stacked bar charts. For these I did something different. I first went back to the spot in the code where i build the chart config. and after I checked the datasets, I put the resulting datasets for the stacked bar chart into a var called myDatasets. This also had a label and background color, but we just care about the data. So I pull out the data values and destructure them into 3 arrays, then add them together and make a new total dataset. In the case of this particular chart there are three datasets always for this stack, so i pull those three arrays out and make a new one by adding each of the items together based on index. so if it's a week, this will add all the mondays, then all the tuesdays, etc and at the end you'll have 'total' which will have a week with the total for each day.

```JavaScript

//destructure out data arrays
let [a,b,c] = myDatasets.map(x=>x.data)
//add values in array together on each index for daily totals
const total = a.map((v,i) => v + b[i] + c[i])
myDatasets.push({
  label: 'Total',
  showLine: false,
  data: total,
  type: 'line',
  pointRadius: 0,
})

```

As you can see above, I then push a new dataset onto the collection for chartjs, call it label, and set the showline false and pointradius 0. i think the point radius isn't needed really, but it's there so i left it. So *really* we are not making a barchart label, we are making a line chart label on an invisible line that will be placed just above the top of the charts. See the code below for these details.

{% gist c19b5ed165ff1a93791cc6ddc7157234 StackedBarChartLabels.js %}

### Thoughts

> That seems... kinda crazy, no?

Yeah, I agree with you. But it does work and I think it's ultimately a lot less code than adding in the data label plugin. Not something to copy/paste around maybe, but I don't have a lot of need for this so this was a pretty simple tweak. This was kind of born out of me just walking through the natural steps of what i'd like to do. I have a stacked bar chart and i want labels. ok, how do i get the sum values, well I'll make another dataset, how can i hide that, well you can't hide it with a bar because the stacking will give you a wrong value, so i can just make it a combo chart, hide the line, and float it a little.

It's also worth noting that with additional tweaking this could easily be made a global plugin and just registered once vs inline. In my case I just imported the one I wanted for the chart that was going to use it initially. But, do we need a plugin? Do we have any other options? Well, there are probably a million ways I guess to do this, but the other contender that I found that seemed to work well was just doing this via animation....

## Animation

So a common example I found for doing stuff like this leveraged the onComplete hook for animation. You can see an example of this below for a stacked bar chart. There are some differences which I'll go through here rather than in code comments.

1. So the first thing to note is that these events are passed animation objects. you have to get the chart off of that object as the chart is not passed directly to these events.
2. I am "iterating" through the datasets and filtering by the one with a label of 'Total'. This is that new line chart that is hidden and added as mentioned above for the plugin method. There will always be one, but I am just filtering and iterating here.
3. I happen to know that the meta key is 3 here so that's hard coded. pretty sure this would never get a great idea, but for some reason i couldn't find the controller to call the meta by index for this object and i didn't go back and refactor this after i switched over to plugins.
4. I also have some values hard coded instead of calculated.

{% gist c19b5ed165ff1a93791cc6ddc7157234 Animation.js %}

Other than the items listed above, it's basically the same thing. So originally I had things setup this way without some of the extra stuff in there for formatting etc. and that worked fine. So why not use this? Well I could. I'm not sure if there is a concrete reason to use one over the other. Basically you could use the same function for either of them and just pass the chart object over to it and it should work. It mostly has to do with when and how you want to deal with the animation. 

# Conclusion

One benefit with plugins is you can register them globally, although I'm not sure if that really makes much of a difference. I'd like to think it would, but I can't say for sure. I like the animation in the charts, so one benefit of doing the plugin is you can use other event triggers other than when the animation completes and maybe decouple them some. I already do this for my vertical line sunday thing. Is that better? I dunno. Honestly I'm not sure that the animation adds much value here. It looks kind of cool briefly, but these are some boring charts and this isn't for an audience that wants extra bells and whistles just for the sake of lines shifting smoothly into place. If you disable animation, it's pretty easy to just leave this in the animation oncomplete. But if you want hover over tool tips and things, it seems better to use the plugin method. I may use them both from time to time, but I thought I would write a post about it because it was interesting getting this stuff to work regardless.