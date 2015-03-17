---
layout:     post
title:      "Predict user's activity from an accelerometer"
subtitle:   "Recognize user's activity with data from an accelerometer"
date:       2015-03-15 14:00:00Z
author:     "lpr"
header-img: "img/"
---
<script src="//api0.cityzendata.net/widgets/quantumviz/dependencies/webcomponentsjs/webcomponents.js"></script>
<link rel="import" href="//api0.cityzendata.net/widgets/quantumviz/czd-quantumviz.html">

The availability of acceleration sensors creates exciting new opportunities for data mining and predictive analytics applications.
In this post, we consider data from accelerometers to perform activity recognition. And thanks this learning we want to identify the physical activity that a user is performing. Several possible applications ensue from this : activity reports, calories computation, alert sedentary, match music with the activity...To resume a lot of applications to promote and encourage health and fitness.
This post is inspired from the [WISDM Lab’s study](http://www.cis.fordham.edu/wisdm/index.php) and data come from [here](http://www.cis.fordham.edu/wisdm/dataset.php).

<h1>Data description</h1>
We use labeled accelerometer data from users thanks to a device in their pocket during different activities (walking, sitting, jogging, ascending stairs, descending stairs, and standing).
The accelerometer measures acceleration in all three spatial dimensions as following :
<ul>
	<li>Z-axis captures the forward movement of the leg</li>
	<li>Y-axis captures the upward and downward movement of the leg</li>
	<li>X-axis captures the horizontal movement of the leg</li>
</ul>

The plots below show characteristics for each activity. Because of the periodicity of such activities, a few seconds windows is sufficient.

<div class="image"><img src="http://127.0.0.1:4000/img/accelerometer_walking.jpg"  alt="Walking activity"></div>
<div class="image"><img src="http://127.0.0.1:4000/img/accelerometer_jogging.jpg"  alt="Jogging activity"></div>
<div class="image"><img src="http://127.0.0.1:4000/img/accelerometer_upstairs.jpg"  alt="Upstairs activity"></div>
<div class="image"><img src="http://127.0.0.1:4000/img/accelerometer_downstairs.jpg"  alt="Downstairs activity"></div>
<div class="image"><img src="http://127.0.0.1:4000/img/accelerometer_standing.jpg"  alt="Standing activity"></div>
<div class="image"><img src="http://127.0.0.1:4000/img/accelerometer_sitting.jpg"  alt="Sitting activity"></div>


The understanding of these graphics are essential to notice patterns for each activity and then recognize it.
For example we observe repeating waves and peaks for the following repetitive activities walking, jogging, ascending stairs and descending stairs.
We also observe no periodic behavior for more static activities like standing or sitting, but different amplitudes.

<h1>Determine and compute features for the model</h1>
EEach of these activities demonstrate characteristics that we will use to define the features of the model.
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

Now let’s use Einstein to compute all of these features !

<div class="image"><img src="http://127.0.0.1:4000/img/accelerometer_einstein.jpg"  alt="Einstein"></div>

No...Not this one...

<h1>Just few words about Einstein</h1>
Einstein is our language which allows to manipulate Geo Time Series and make statistical computations. It is composed by several frameworks and a thousand of functions.

<h3>Bucketize framework</h3>

The BUCKETIZE framework provides the tooling for putting the data of a Geo Time Series into regularly spaced buckets.

<h3>Mapper framework</h3>

The MAP framework allows you to apply a function on values of a Geo Time Series that fall into a sliding window.

<h3>Reduce framework</h3>

The REDUCE framework operates on equivalence classes forming a partition of a set of geo time series.

<h1>Features computation with Einstein</h1>
Let’s use Einstein to compute all of these features !

<h3>Average acceleration and Standard deviation</h3>

	false // Bessel correction
	MUSIGMA 
	'standev_x' STORE 
	'mean_x' STORE


<h3>Average absolute difference</h3>

	$data // call the data
	DUP   // duplicate the data. Don't forget Einstein use a stack

	// compute the mean
	bucketizer.mean
	0 0 1
	5 ->LIST 
	BUCKETIZE 
	VALUES LIST-> DROP LIST-> DROP 'mean' STORE

	// Here we do : x - mean for each point x
	-1 $mean * // multiply by -1
	mapper.add // and add this value 
	0 0 0
	5 ->LIST
	MAP

	// Then apply a absolute value : |x - mean|
	mapper.abs
	0 0 0
	5 ->LIST
	MAP

	// And compute the mean : (1 / n )* sum |x - mean|
	// where n is the lenth of the time series
	bucketizer.mean
	0 0 1
	5 ->LIST 
	BUCKETIZE 
	// store the result
	VALUES LIST-> DROP LIST-> DROP 'avg_abs_x' STORE


<h3>Average resultant acceleration</h3>

	$data

	// Compute the square of each value
	2.0
	mapper.pow
	0 0 0
	5 ->LIST
	MAP

	// Now add up !
	[] // create one equivalence class with all Geo Time Series
	reducer.sum
	3 ->LIST
	REDUCE // it return only one GTS

	// Then compute the root square : √(x² + y² + z²)
	0.5
	mapper.pow
	0 0 0
	5 ->LIST
	MAP

	// And apply a mean function : 1/n * sum [√(x² + y² + z²)]
	bucketizer.mean
	0 0 1
	5 ->LIST
	BUCKETIZE
	// store the returned value
	VALUES LIST-> DROP LIST-> DROP 'res_acc' STORE


<h3>Average time between peaks</h3>

	$data
	DUP

	// Now let define the maximum
	bucketizer.max 
	0 0 1   // lastbucket bucketspan  bucketcount
	5 ->LIST
	BUCKETIZE // return a GTS

	// extract the max value and store it
	VALUES LIST-> DROP LIST-> DROP 'max_x' STORE

	// keep data point for which the value is greather than 0.9 * max
	$max_x 0.9 *
	mapper.ge // ge i.e greather or equal
	0 0 0
	5 ->LIST
	MAP

	// just return the tick of each datapoint
	mapper.tick 
	0 0 0              
	5 ->LIST
	MAP

	// compute the delta between each tick
	mapper.delta
	1 0 0
	5 ->LIST
	MAP

	// keep it if the delta is not equal to zero
	0
	mapper.ne
	0 0 0
	5 ->LIST
	MAP

	// compute the mean of the delta
	bucketizer.mean
	0 0 1
	5 ->LIST
	BUCKETIZE

	// and store the value
	VALUES LIST-> DROP LIST-> DROP 'peak_x' STORE


<h1>Decision Tree : Random Forest and Boosting methods</h1>
After aggregating all these data, we use a training data set to create predictive models using classification algorithms (supervised learning). And then we involve predictions for the activity performing by users.
Here we choose the implementation of the Random Forest method and Gradient-Boosted Trees using MLlib.

Here the code.

INSERT CODE

<h1>Conclusion</h1>