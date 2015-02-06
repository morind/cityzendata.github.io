---
layout:     post
title:      "Behind the colors of the CES"
subtitle:   "From an abstract time-series platform to a concrete chromatic experience"
author:     horacio
header-img: "img/post-bg-01.jpg"
---


<script src="http://www.cityzendata.com/colors/webcomponentsjs/webcomponents.js"></script>
<link   rel="import" href="http://www.cityzendata.com/colors/polymer/polymer.html">
<link href='http://fonts.googleapis.com/css?family=Comfortaa' rel='stylesheet' type='text/css'>
<link rel="import" href="http://www.cityzendata.com/colors/czd-colorhistogram/czd-colorhistogram.html">
<link rel="import" href="http://www.cityzendata.com/colors/czd-colormonitor/czd-colormonitor.html">



As we [told you]({% post_url 2015-01-06-CES-colors %}), last month we were present at <a href="http://www.cesweb.org/">#CES2015</a> in Las Vegas, sharing a booth with our sister company [Cityzen Sciences](http://www.cityzensciences.fr/).

For us one of the challenges going to CES was how to make CES attendees to understand our offering, as our services are mainly orientated to developers.
We had thought about presenting some user cases, but it seemed to us that in CES people are more receptive to physical objects than to lengthly explanations, so we decided to build an interactive experience mixing physical objects and online components. 

After some brainstorming, the idea of **#color** began to take shape. We would have a physical control panel in one side, and a orb in another, and visitors could change the color of the orb by playing with the buttons on the control panel. Easy enough, everybody would think... until we show them that the control panel and the orb aren't connected, they interact with the Cityzen Data platform, the control panel by pushing user chosen color and the orb by reading it and changing its light color accordingly.

With this first part of the experiment, we would be able to show the potential of our platform to act as a hub for home automation, but it wouldn't reveal its possibilities in data analytics, and how this analytics capabilities allows to push way further than simple command-and-control home automation. So we decided to an online component with several widgets showing different analytics on the colors that the CES attendees have selected. These widget should be simple visualization blocks, with all the analytics done in the platform.

So with these ideas in mind, we began to craft our **#colors*** demo...

<div class="image"><img src="{{ site.url }}/img/behind-CES-colors-00-1024px.jpg"  alt="Behind the colors of the CES"></div>

## The control panel ##

<div class="image small"><img src="{{ site.url }}/img/behind-CES-colors-02-1024px.jpg"  alt="Behind the colors of the CES"></div>



## The orb ##

<div class="image "><img src="{{ site.url }}/img/behind-CES-colors-03-1024px.jpg"  alt="Behind the colors of the CES"></div>


## The web application ##

The online part is done in HTML/CSS/JS using a web component approach. Each widget is an independent component that can be used in its own or combined with the others to craft the complete application.


<div class="image"><img src="{{ site.url }}/img/behind-CES-colors-01-1024px.jpg"  alt="Behind the colors of the CES"></div>


## And what did we get? ##

Here your have the **Hue popularity** widget, you can use it to get an insight of the popularity of the different shades of colors in our visitors. For each one of its setting, the widgets calls the Cityzen Data platform with a pre-built set of commands in Einstein, our data manipulation environment. Using these Einstein commands, we ask the platform to read the data corresponding to the chosen  period, apply a custom-made RGB to HSV transformation and calculate the histogram of the hue distribution. The widget receives the this histogram and plots it on the screen.

<div class="flex">
  <czd-colorhistogram></czd-colorhistogram>
</div>

At a first glance you could say that, according to our measures, the most popular [hue](http://en.wikipedia.org/wiki/Hue) for our CES visitors was a shade of yellow, the hue value 59 (RGB secondary yellow has a hue value of 60). If we try to go further in our analysis, we notice an intriguing feature: hue popularity isn't uniformly distributed, there are well defined picks around some colors: RGB primary red, green and blue, and RGB secondary cyan. Why were those iconic colors chosen more often than other ones? They are really more popular colors than humbler orange, violet or lime green?

Truth was in data, of course, and it was far more prosaic: our control panel indirectly biased the choice of colors. Next week I'll tell you how we arrived to that conclusion and I'll show you how we used Einstein to find the proof.
