---
layout:     post
title:      "ePunchingBag"
subtitle:   "a connected experience for the Startup Weekend Brest"
author:     "seb"
author2:    "horacio"
---

For the last several months, besides our work at Cityzen Data, we have been rather busy organizing the 2015 edition of the [Startup Weekend Brest](http://brest.startupweekend.org/).

While preparing the event, the organisation team brainstormed about some data-oriented fun activity to propose to the startupers during the launch time.

After many unsatisfactory proposals, I got an idea: we could make a DIY *Connected Punching Bag*, with some basic bricks as an accelerometer, an Arduino and a Raspberry Pi.
At this stage of the project, we hoped to collect a measure of the force of each punch, and build a sort of ranking. Then Horacio suggested to add a camera to film the punch and send it to YouTube, and a barcode reader to allow us to identify the puncher, and the [ePunchingBag](https://twitter.com/ePunchingBag) project was born. The team was enthusiast, and we all decided to go for it, in the maker way!   


## Part one : Hack a Punching ball ##

The objective was to get a measure of the hitting strength by measuring the acceleration of the punching bag, so our first task was set up a way to record that acceleration. 

In order to record the acceleration, we used a 3 axis numeric accelerometer linked via I2C to an Arduino Nano. The accelerometer gave us the 3D acceleration vector, and by calculating its norm, we could have the acceleration we needed. The Arduino get the raw data from the accelerometer, compute its norm and send it via serial on USB to a Raspberry PI.  This one acts as a gateway, sending directly all the received data on USB to the Cityzen Data platform via HTTP.

[![ePunchingBag in action](https://farm8.staticflickr.com/7442/16230847037_31731c64bb_z.jpg)](https://www.flickr.com/photos/114768676@N07/16230847037)

*Crédit photo [Nicolas Ollier](https://twitter.com/nikko2foo)*


## Part two : Smile you’re being filmed ##

Each Startuper had a badge with a barcode. Next to the ePunchingBag a barcode scanner was linked to the Raspberry PI. Each valid barcode scan launch a video record of 8 seconds, time good enough to hit one or several times in the ePunchingBag

![the RaspPi camera](/img/ePunchingBag-02_640px.jpg)

*The camera set-up*


## Rush hour ##

We wasn’t sure how many of the 120 startupers would be tempted by our makeshift game. Our surprise was complete, with more than 80 videos recorded the first day!

In order to match the expectation, we needed to build a small app to show the video and data from the ePunchingBall at the end of the event. We worked hard on Saturday’s night to code a demo application showing the last 10 hits, with the video, the identity of the startuper and the  force of the hit.

The [demo](https://api0.cityzendata.net/widgets/punchingball/) was build with [Polymer](http://polymer-project.org) web-components and some Einstein scripts.



![ePunchingBag team](/img/ePunchingBag-01_640px.jpg)

*The ePunchingBag team and friends*

