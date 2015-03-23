---
layout:     post
title:      "Data manipulation with impulse sensors"
subtitle:   "Timestamp is as important as the sensor value"
author:     "seb"
---
<script src="//api0.cityzendata.net/widgets/quantumviz/dependencies/webcomponentsjs/webcomponents.js"></script>
<link rel="import" href="//api0.cityzendata.net/widgets/quantumviz/czd-quantumviz.html">

Impulse sensors are often used to measure gas, electricity or water consumption.
Each impulse corresponds to a quantity consumed (volume, watt per hour etc…).

This type of sensors are useful for billing systems or comparing two time-slots of consumption. 
I take as example one DIY impulse reader from an electricity meter installed into my home. 

![DIY impulse sensor](/img/tickToPower-01_640px.jpg)

Each impulse represents 1WH consumed. For each impulse we store the timestamp related. 
The sensor also acts as an accumulator. The global trend (day by day) looks like that.

All graphics in this article uses Cityzen Data visualization widget QuantumViz, a Polymer webcomponent designed for visualizing data from Cityzen Data platform.

<czd-quantumviz width="500" height="400" tooltip="false"> 
'mrKvnoIttxEpVO5IxbsWvfjkg6gzqTMiF5a7JeHIepZu440snQEAneCUGAZ4JKKOVOLyD7evxGrWkfOcMh1lKRBoqptPjwbB4SOhT_j1ufAg4k119Ka.y5wJORGJ87IC0P1YLBwmoDpB94JVCY52zOoDHV78sP4dJ3f.K_nG7dm1vxglIqD4k6pIJ_LYbrzLnisTo_RmHH3OLqNrrIG_cgFkH_0fdKfpN37_Ms_NTT0RHwbHz5sSst21dALLNuv55b4DEbA2OINMwKymyAn.1mLEMkAV.toMk6fBi0HY23FhLJVzA.IAQuLNtWc9mzbLXGcc9RWYrvlPZiw4K2ZCBvaJb50BC3GwEmM4SDuYxJDYXQxcTTrfUpAz4SP5XVqYPWlRT_Q1Y33'
EVALSECURE

// SUM CONSUMPTION BY HOUR
bucketizer.first
0 'P1D' DURATION 0  // 1 DAY bucketspan
5 ->LIST BUCKETIZE
'gts' STORE

'color' '#ff1010'
'key' 'WH accumulation '
4 ->MAP
1 ->LIST
'params' STORE

'interpolate' 'linear' 2 ->MAP
'globalParams' STORE

'gts' $gts
'params' $params
'globalParams' $globalParams
6 ->MAP
</czd-quantumviz>

#### Einstein code #####
In this case bucketize is usefull to reduce the number of points displayed in the browser.

    bucketizer.first // take the first value in the bucket
    0 
    'P1D' DURATION  // 1 DAY bucketspan
    0
    5 ->LIST BUCKETIZE

## Part one : bucketize data is simple  ##

We can easily sum the energy consumed per hours for the last 24 hours with the Cityzen Data bucketize framework.

<czd-quantumviz width="500" height="400" tooltip="false"> 
'Ixlv_wanKqLaW.Q8hJFhnapicj3zxcFndZYLbsXwks8auv1SGbcmlgWaxLsxLdNqU7Ws16yVGZWcrcnURUFmunLC0Nv5oCDXCUlNBznNifBrqh8N9LegJzl.FrlulEDckaI7S8BjjuD1XWhgtKuTynCkPAtVGQYZfWggv12RgFtLQ6Vocft0RMXUsz.I9CkMQb8iRB_JMPuykxjvxLJDaRVoPcEMciiZqoUE81T..jvF0VWfZMSoA7zpj8h48NyJ4qUgsgLYsUaxmzrU083lXIwaELSvzmPTLZjCWmH36m8ZDWEXY2FMNWu0Whq_lhHtRLODIw08BsFBbrkCEsG_0A6K1Fa85.pOHAGNRtnJICW207vaIPRR3.'
EVALSECURE

// compute the delta between each ticks
mapper.delta
1 0 0 5 ->LIST MAP

// SUM CONSUMPTION BY HOUR
bucketizer.sum
0 'P1H' DURATION 0  // 1 HOUR bucketspan
5 ->LIST BUCKETIZE
'gts' STORE

'color' '#ff1010'
'key' 'WH Consumed by Hour'
4 ->MAP
1 ->LIST
'params' STORE

'interpolate' 'linear' 2 ->MAP
'globalParams' STORE

'gts' $gts
'params' $params
'globalParams' $globalParams
6 ->MAP
</czd-quantumviz>

#### Einstein code ####
The number of WH consumed per tick is extracted with the mapper framework

    // compute the WH consummed between each ticks
    mapper.delta
    1 0 0 5 ->LIST MAP

This give you an overview of your electric consumption, but not a detailed analysis. In this electric consumption sample, a convection heater is not working properly. It starts to many times for few seconds.
Even if you bucketize the time series per minute, it is not easy to apply analysis patterns in order to detect default.

<czd-quantumviz width="500" height="400" tooltip="false">
'.HvF1dIDdrNQzPuT2CcHSC4G5pANO0u5OV0bdaYxMSypFjqs0l8LNBsTFTMImxEjADC4pvKQvtmgqwYweDreqaScOjN.ReF1tXy2nkO1HBQiw2CaWDW7BXOo4k0d4H1LeGehpsIj1j.FhS5cb32Ax47deWOGPeHpLKKiIjhuvUFD78h.pHbcxkwY7U0aZJDKK9KnhvintyP2jNkSMgiMrAI8MEwuSNy_qXLkJvF0ezPF4imthPER4O61Jl837IAij1jWPXC8aKQzxR98GoLerqwcB4HV_W0p615npek1CLXz7HGClyqoD0WgRvGkZY8oO9dhpZ2aSDb691LFFF9zbsI2oNZ7KvEcDCXJWXb4XQ7'
EVALSECURE

mapper.delta
1 0 0 5 ->LIST MAP

bucketizer.sum
0 'P0H1M' DURATION 0
5 ->LIST BUCKETIZE
'gts' STORE

'color' '#ff1010'
'key' 'WH Consumed by Minute'
4 ->MAP
1 ->LIST
'params' STORE

'interpolate' 'linear' 2 ->MAP
'globalParams' STORE

'gts' $gts
'params' $params
'globalParams' $globalParams
6 ->MAP
</czd-quantumviz>

#### Einstein code ####
Bucketiser framework is still useful for compute electric consumption per minute 

    bucketizer.sum
    0 'P0H1M' DURATION 0
    5 ->LIST BUCKETIZE

A better way is to work on the power curve in order to detect when the convection heater is running or not.
We don’t have directly this information but we can rebuild it from the impulse sensor timestamp. 

## Part two : extract data from the timestamp ##

Remember that each impulse are timestamped, so delta time between two impulses give us how much time we took for consume 1WH.
We can simply deduce power with the division below.

    1WH / DeltaT = Power
    DeltaT = time took to consume this wh

Apply this division on the whole time series will produce power time series of the electric consumption.

<czd-quantumviz width="500" height="400" tooltip="false">
'.HvF1dIDdrNQzPuT2CcHSC4G5pANO0u5OV0bdaYxMSypFjqs0l8LNBsTFTMImxEjADC4pvKQvtmgqwYweDreqaScOjN.ReF1tXy2nkO1HBQiw2CaWDW7BXOo4k0d4H1LeGehpsIj1j.FhS5cb32Ax47deWOGPeHpLKKiIjhuvUFD78h.pHbcxkwY7U0aZJDKK9KnhvintyP2jNkSMgiMrAI8MEwuSNy_qXLkJvF0ezPF4imthPER4O61Jl837IAij1jWPXC8aKQzxR98GoLerqwcB4HV_W0p615npek1CLXz7HGClyqoD0WgRvGkZY8oO9dhpZ2aSDb691LFFF9zbsI2oNZ7KvEcDCXJWXb4XQ7'
EVALSECURE

// replace the value by the tick timestamp
mapper.tick
0 0 0 5 ->LIST MAP

// compute the delta time between each ticks
mapper.delta
1 0 0 5 ->LIST MAP

// Compute the powser P=1HOUR(µs) / deltaTime
// set 0 when the time=0 in order to avoid an aritmetique exp
// Apply the macromapper on each ticks
<%
'_list' STORE       // We begin by storing the list
$_list 0 GET        // We get the tick
NaN NaN NaN         // We add NaN for positions and elevation
$_list 7 GET 0 GET  // compute the power  P= 1HOUR / deltaT
<% 0 != %>
<% 
3600000000
$_list 7 GET 0 GET 
/
%>
<% 0 %>
IFTE
%>           
MACROMAPPER
0 0 0 5 ->LIST MAP
'gts' STORE

'color' '#ff1010'
'key' 'Power (W)'
4 ->MAP
1 ->LIST
'params' STORE

'interpolate' 'linear' 2 ->MAP
'globalParams' STORE

'gts' $gts
'params' $params
'globalParams' $globalParams
6 ->MAP
</czd-quantumviz>

As you can see on the graphic above, the power curve quality is widely better than the previous graphic.

#### Einstein code ####
This part is more complicated, we have to apply different mappers. 
Firstly, extract ticks (timestamps)

    mapper.tick
    0 0 0 5 ->LIST MAP

Secondly, get the delta time between the ticks

    mapper.delta
    1 0 0 5 ->LIST MAP

Thridly, use a macro mapper (custom mapper) for compute the power

    <%
      '_list' STORE  // We begin by storing the list
      $_list 0 GET   // We get the tick
      NaN NaN NaN    // No positions and elevation
      $_list 7 GET 0 GET  // compute the power  P= 1HOUR / deltaT
      <% 0 != %>
      <% 
         3600000000
         $_list 7 GET 0 GET 
         /
      %>
      <% 0 %>
      IFTE
    %>           
    MACROMAPPER
    0 0 0 5 ->LIST MAP

## Conclusion ##

Electric energy disaggregation is one of the most difficult analysis challenge, it is not the purpose of this paper.

If you plan to make data analysis with impulse sensor, considere timestamp as one of the most accurate data.

Please remember that saving the timestamp of each impulse will give you precious informations.
