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

With this first part of the experiment, we would be able to show the potential of our platform to act as a hub for home automation, but it wouldn't reveal its possibilities in data analytics, and how this analytics capabilities allows to push way further than simple command-and-control home automation. To do it, we chose to code an online component with several widgets showing different analytics on the colors that the CES attendees have selected. These widget should be simple visualization blocks, with all the analytics done in the platform.

So with these ideas in mind, we began to craft our **#color** demo...

<div class="image"><img src="{{ site.url }}/img/behind-CES-colors-00.jpg"  alt="Behind the colors of the CES"></div>

## The control panel ##

To give our visitors the choice of the color, we prepared a rather simple control panel, with three pushbuttons and three SPCO ([Single pole, centre off](http://technologymash.blogspot.fr/2010/10/switch-basics.html)) switches. 

<div class="image small"><img src="{{ site.url }}/img/behind-CES-colors-02.jpg"  alt="Schema of the control panel"></div>

The pushbuttons allowed visitors to quickly choose a color: black for the left button, white for the right one, and a random color with the middle pushbutton. Each one of the SPCO switches controlled one component of the RGB color definition: with the left one visitors increased or decreased the red component, the green component with the middle switch and the blue component with the right one. Buttons and switches were unmarked, as discovering their function by seeing their effect was part of the experience.

<div class="image"><img src="{{ site.url }}/img/behind-CES-colors-04.jpg"  alt="The #color control panel"></div>

The pushbuttons and SPCO switches  were connected to a [Raspberry Pi](http://www.raspberrypi.org/) that sent the metrics to the Cityzen Data platform.

## The orb ##

For the orb of light we used a RGB LED connected to another Raspberry Pi. The Raspberry Pi opens a web-socket to the Cityzen Data platform and it's notified of any changes to the chosen color, instructing then the LED to change accordingly.

<div class="image"><img src="{{ site.url }}/img/behind-CES-colors-05.jpg"  alt="The #color control panel"></div>

## The web application ##

The online part is done in HTML/CSS/JS using a web component approach. Each widget is an independent component that can be used in its own or combined with the others to craft the complete application.

<div class="image"><img src="{{ site.url }}/img/behind-CES-colors-01.jpg"  alt="Behind the colors of the CES"></div>


The first widget is composed of a *colorgram*, that shows in real time the last five minutes of chosen colors, and a line chart that tracks RGB components for this last five minutes. When the widget is loaded it uses an Einstein script to ask the platform for the las 5 minutes of color data to initialize itself, then it opens a web-socket connection to get notified of any further change on the chosen color.

<div class="image small"><img src="{{ site.url }}/img/behind-CES-colors-06.jpg"  alt="Colorgram"></div>

The second one is the *hue popularity* widget that we used to track the hues of colors chosen by our visitors and to try to deduct their favorite tints of color. For each one of its setting, the widgets calls the Cityzen Data platform with a pre-built set of commands in Einstein, our data manipulation environment. Using these Einstein commands, we ask the platform to read the data corresponding to the chosen  period, apply a custom-made RGB to HSV transformation and calculate the histogram of the hue distribution. The widget receives the this histogram and plots it on the screen. 

<div class="image small"><img src="{{ site.url }}/img/behind-CES-colors-07.jpg"  alt="Histogram"></div>

We also had a password-protected third widget to mimic the behavior of the control panel, allowing us to control the orb via the smartphone.

<div class="image small"><img src="{{ site.url }}/img/behind-CES-colors-08.jpg"  alt="Control panel"></div>



## And what did we get? ##

Here your have the **Hue popularity** widget, you can use it to get an insight of the popularity of the different shades of colors in our visitors. 

<div class="flex">
  <czd-colorhistogram></czd-colorhistogram>
</div>

At a first glance you could say that, according to our measures, the most popular [hue](http://en.wikipedia.org/wiki/Hue) for our CES visitors was a shade of yellow, the hue value 59 (RGB secondary yellow has a hue value of 60). If we try to go further in our analysis, we notice an intriguing feature: hue popularity isn't uniformly distributed, there are well defined picks around some colors: RGB primary red, green and blue, and RGB secondary cyan. Why were those iconic colors chosen more often than other ones? They are really more popular colors than humbler orange, violet or lime green?

<div class="image small"><img src="{{ site.url }}/img/behind-CES-colors-03.jpg"  alt="Colorgram"></div>

Truth was in data, of course, and it was far more prosaic: our control panel indirectly biased the choice of colors. As a more detailed analysis confirmed, many people used the white or the black pushbuttons to set an initial state and then only one of the switch to change one of the RGB components, generating colors whose hue is almost identical to that of primary or secondary RGB colors. In the precedent image you can see this pattern, as the user began with the black color and pushed up the red RGB component until the full primary red.


