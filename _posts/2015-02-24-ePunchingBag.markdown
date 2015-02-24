---
layout:     post
title:      "ePunchingBag"
subtitle:   "A connected experience for the Startup Weekend Brest"
author:     "seb"
author2:    "horacio"
---

For the last several months, besides our work at Cityzen Data, we have been rather busy organizing the 2015 edition of the [Startup Weekend Brest](http://brest.startupweekend.org/).

While preparing the event, the organisation team brainstormed about some data-oriented fun activity to propose to the startupers during the launch time.

After many unsatisfactory proposals, I got an idea: we could make a DIY *Connected Punching Bag*, with some basic bricks as an accelerometer, an Arduino and a Raspberry Pi.
At this stage of the project, we hoped to collect a measure of the force of each punch, and build a sort of ranking. Then Horacio suggested to add a camera to film the punch and send it to YouTube, and a barcode reader to allow us to identify the puncher, and the [ePunchingBag](https://twitter.com/ePunchingBag) project was born. The team was enthusiast, and we all decided to go for it, in the maker way!   


![ePunchingBag team](/img/ePunchingBag-01_640px.jpg)

*The ePunchingBag team and friends*


## Part one : Hack a Punching ball ##

The objective was to get a measure of the hitting strength by measuring the acceleration of the punching bag, so our first task was to set up a way to record that acceleration. 

In order to record the acceleration, we used a 3 axis numeric accelerometer linked via I2C to an Arduino Nano. The accelerometer gave us the 3D acceleration vector, and by calculating its norm, we could have the acceleration we needed. The Arduino get the raw data from the accelerometer, compute its norm and send it via serial on USB to a Raspberry PI.  This one acts as a gateway, sending directly all the received data on USB to the Cityzen Data platform via HTTP.

[![ePunchingBag in action](https://farm8.staticflickr.com/7442/16230847037_31731c64bb_z.jpg)](https://www.flickr.com/photos/114768676@N07/16230847037)

*Crédit photo [Nicolas Ollier](https://twitter.com/nikko2foo)*


## Part two : Smile, you’re being filmed ##

Each Startuper had a badge with a barcode. Next to the ePunchingBag a barcode scanner was linked to the Raspberry PI. Each valid barcode scan launch a video record of 8 seconds, time good enough to hit one or several times in the ePunchingBag

![the RaspPi camera](/img/ePunchingBag-02_640px.jpg)

*The camera set-up*


## Rush hour ##

We wasn’t sure how many of the 120 startupers would be tempted by our makeshift game. Our surprise was complete, with more than 80 videos recorded the first day!

In order to match the expectation, we needed to build a small app to show the video and data from the ePunchingBall at the end of the event. We worked hard on Saturday’s night to code a demo application showing the last 10 hits, with the video, the identity of the startuper and the  force of the hit.

The [demo](https://api0.cityzendata.net/widgets/punchingball/elements/czd-punch/index_list.html) was build with [Polymer](http://polymer-project.org) web-components and some Einstein scripts.


![The demo app](/img/ePunchingBag-03_640px.jpg)

*The quick-coded demo app*


## The technical side ##

The brain of the ePunchingBag was composed of two Raspberry Pi computers. The first of them was connected to the Arduino Nano to receive the norm of the acceleration and send the data as a time series to the Cityzen Data platform. The second Raspberry Pi controlled the user identification (via the barcode reading) and the video capture (using the Pi camera) and its upload to YouTube. It also took the user id and the YouTube id of the video, and sent them to the Cityzen Data platform as two time series (user and video ids).

So in the platform we have three independent time series: the punching-bag acceleration, the users id and the video id.

![the time-series](/img/ePunchingBag-timeseries-01.png)

The app used to display the data needed to be as simple as possible. It didn't need to deal with the complexity of the three time series, or with finding a way to transform the acceleration time-series into a score. Fortunately for us, the Cityzen Data platform shines in this kind of tasks. We only needed to prepare a small Einstein script to do it in the platform and get back to the application a nice JSON ready to be used. 

The application sends the Einstein script to the platform, that executes it and gives back to the app the data it needs.  As we wanted to get a score for each punch sequence, we decided to calculate the integral of the acceleration series to have a measure of its energy, and we give back the last point of this integral as the energy score of the sequence. Here you have the Einstein script used.If you are not familiar with our platform, it can seem difficult to understand, but believe me, the learning curve is rather smooth and quick. 


    'READING.TOKEN.HERE'
    'org.startupweekend.brest.punchingball.userId'
    0 ->MAP
    $timestamp -1
    5 ->LIST
    FETCH
    0 GET 'gts_userId' STORE
     
    'READING.TOKEN.HERE'
    'org.startupweekend.brest.punchingball.videoId'
    0 ->MAP
    $timestamp   -1
    FETCH
    0 GET 'gts_videoId' STORE
      
    'READING.TOKEN.HERE'
    'org.startupweekend.brest.punchingball.acc'
    0 ->MAP
    $timestamp 6000000 +           // We have 6 seconds to hit after the ID read
    10000000
    FETCH
    'gts_acc' STORE 

    <% $gts_acc SIZE 0 > %>    // Let's see if we have a punch associated to this id read
    <% 
      $gts_acc 0 GET 'gts_acc' STORE
        
      // Using Einstein INTEGRATE function to calculate the energy of the punch and get the score  
      $gts_acc
      0.0 INTEGRATE   
      'gts_int' STORE 
       
      // And here we have all the information
      $gts_acc $gts_userId $gts_videoId $gts_int 4 ->LIST
      'measure' STORE
    %>
    IFT 
    
    $measure 


For each punch sequence, the Einstein script gave back to our application the user id, the YouTube video id, the acceleration for the sequence and its energy score. That info, some Polymer components and the app were ready to show the fun of the ePunchinBag.

<iframe width="640" height="360" src="https://www.youtube.com/embed/m4bAZX2LP8A" frameborder="0" allowfullscreen></iframe>

According to the Startup Weekend attendees, the ePunchingBag a fun object to play with and a nice stress reliever (something very important during a Startup Weekend, as any attendee or organization team member can tell you). For us it was a fun project to build, and it gave us lots of ideas for more crazy side projects for next events. For the moment, we are going to present the next version of the ePunchingBag in the next [Maker Faire St Malo](http://www.makerfairesaintmalo.com/).