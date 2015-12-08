---
layout:     post
title:      "Simplifying IoT applications"
subtitle:   "leveraging Cityzen Data's IoTics platform to decode packed data frames"
author:     "hbs"
---
### IoT networks ###

The Internet of Things (IoT) is all about bridging the gap between the digital world and the physical world (sensors, actuators, devices large and small, ...). To make those two worlds communicate, you need a network! And given some of the devices out there operate on small batteries, you need a network which does not require too much energy to transmit or receive data. Unfortunately, the traditional network options such as WiFi or 3G fail miserably that energy test. But rejoy, there are some networks which are being rolled out which were designed specifically for this energy test. Those networks are usually low bandwidth but it is still sufficient for lots of applications.

Those networks dedicated to the IoT will be used by the devices to transmit frames of data which convey information in a packed format to optimize the sparse bandwidth. When we say *packed*, we mean it, the format is optimized to the bit level so no extraneous data is sent on the air. This is great for energy consumption since the time spent sending data is greatly reduced, but it also means that you have to do the unpacking before you can actually use the data from your devices in your application.

### All your unpacking are belong to us ###

Unpacking the data means checking integrity, decrypting, and finally extracting the informations from the raw bits. All those operations require some programming and infrastructure to run on, and that's on top of other infrastructure you need to deploy to store the decoded informations (typically a time series database) and your application backend!

Well the good news is that you can do most of those operations within the Cityzen Data Warp platform, a time series database on steroids. We provide a data manipulation environment based on a language (Einstein) dedicated to working with time series, and among the functions offered by Einstein you will find some to check LoRa MIC and decrypt LoRa frames, to do bit fiddling with the decoded data and to store the decoded information as new time series. This means that you won't need to deploy specific infrastructure to handle the frames coming from your IoT network provider, you just have to give instructions so the frames are stored on a specific time series on the Cityzen Data platform, and voilà!

### Compatible providers ###

We are constantly adding support for new networks to make life easier for our customers. LoRa is the buzzing technology these days for IoT networks, so we are working with LoRa network operators and network equipment manufacturers to ensure our offerings can work together.

This is a partial list of companies we work with:

- <a href="http://www.actility.com/">Actility ThingPark</a>
- <a href="http://kerlink.fr/">Kerlink</a>
- <a href="http://ackl.io/">Ackl.io</a>

Beyond LoRa we are also compatible with the SigFox IoT network.

### Working together ###

If you are an IoT network operator or equipment manufacturer, we would love to talk to you to add you to the list above. Integration is usually very easy if you have a callback mechanism to forward frames to your customers' datacenters.

If you are the user of an IoT network, tell us what you are trying to achieve and we'll see what we can do to make you more productive with your IoT application!

You can reach us via Twitter <a href="https://twitter.com/CityzenData">@CityzenData</a> or via email at <a href="mailto:contact@cityzendata.com">contact@cityzendata.com</a>
