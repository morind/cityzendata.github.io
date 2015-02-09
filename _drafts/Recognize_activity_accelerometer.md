---
layout:     post
title:      "Data from an accelerometer"
subtitle:   "Recognize user's activity with data from an accelerometer"
date:       2015-02-05 14:00:00Z
author:     "lpr"
header-img: "img/"
---

The availability of acceleration sensors creates exciting new opportunities for data mining and predictive analytics applications.
In this post, we consider data from accelerometers to perform activity recognition. And thanks this learning we want to identify the physical activity that a user is performing. Several possible applications ensue from this : activity reports, calories computation, alert sedentary, match music with the activity...To resume a lot of applications to promote and encourage health and fitness.
This post is partialy based of the WISDM Lab’s study and data sets come from here.

<h1>Data description</h1>
We use labeled accelerometer data from users thanks to a device in their pocket during different activities (walking, sitting, jogging, ascending stairs, descending stairs, and standing).
The accelerometer measures acceleration in all three spatial dimensions as following :
<ul>
	<li>Z-axis captures the forward movement of the leg</li>
	<li>Y-axis captures the upward and downward movement of the leg</li>
	<li>X-axis captures the horizontal movement of the leg</li>
</ul>

The plots below show characteristics for each activity. Because of the periodicity of such activities, a few seconds windows is sufficient.


ADD PICTURES HERE


The understanding of these graphics are essential to notice patterns for each activity and then recognize it.
For example we observe repeating waves and peaks for the following repetitive activities walking, jogging, ascending stairs and descending stairs.
We also observe no periodic behavior for more static activities like standing or sitting, but different amplitudes.

<h1>Features description</h1>
Each of these activities demonstrate characteristics that we will use to define the features of the model.
For example, the plot for walking shows a series of high peaks for the y-axis spaced out approximately 0.5 seconds intervals, while it is rather a 0.25 seconds intervals for jogging.
We also notice that the range of the y-axis acceleration for jogging is greater than for walking.
And so on.
All these observations are very important to determine features we want to use for our model.

We determine a windows (a few seconds) on which we will compute all these features.
These features are described below :
<ul>
	<li>Average acceleration (for each axis)</li>
	<li>Standard deviation (for each axis)</li>
	<li>Average absolute difference (for each axis)</li>
	<li>Average resultant acceleration (1/n * sum [√(x² + y² + z²)])</li>
	<li>Average time between peaks (max) (for Y-axis)</li>
</ul>


<h1>More about our language Einstein</h1>
Bucketize framework
The BUCKETIZE framework provides the tooling for putting the data of a Time Series into regularly spaced buckets.
Mapper framework
The MAP framework allows you to apply a function on values of a Geo Time SeriesTM that fall into a sliding window.
Reduce framework
The REDUCE framework operates on equivalence classes forming a partition of a set of geo time series.
Other Einstein function

<h1>Features computation with Einstein</h1>
Now let’s use Einstein to compute all of these features !

<h3>Average acceleration</h3>
insert Eintein script

<h3>Standard deviation</h3>
insert Eintein script

<h3>Average absolute difference</h3>
insert Eintein script

<h3>Average resultant acceleration</h3>
insert Eintein script

<h3>Average time between peaks (Y-axis)</h3>


<h1>Decision Tree : Random Forest and Boosting methods</h1>
After aggregating all these data, we use a training data set to create predictive models using classification algorithms 
(supervised learning). And then we involve predictions for the activity performing by users.
Here we choose the implementation of the Random Forest method and Gradient-Boosted Trees using MLlib.

<h1>Conclusion</h1>