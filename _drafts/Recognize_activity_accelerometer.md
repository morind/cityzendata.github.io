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
In this post, we consider data from accelerometers to perform activity recognition. And thanks to this learning we want to identify the physical activity that a user is performing. Several possible applications ensue from this: activity reports, calories computation, alert sedentary, match music with the activity... In brief, a lot of applications to promote and encourage health and fitness.
This post is inspired from the [WISDM Lab’s study](http://www.cis.fordham.edu/wisdm/index.php) and data come from [here](http://www.cis.fordham.edu/wisdm/dataset.php).

<h1>Data description</h1>

We use labeled accelerometer data from users thanks to a device in their pocket during different activities (walking, sitting, jogging, ascending stairs, descending stairs, and standing).

The accelerometer measures acceleration in all three spatial dimensions as following:

<ul>
	<li>Z-axis captures the forward movement of the leg</li>
	<li>Y-axis captures the upward and downward movement of the leg</li>
	<li>X-axis captures the horizontal movement of the leg</li>
</ul>

The plots below show characteristics for each activity. Because of the periodicity of such activities, a few seconds windows is sufficient.

<div class="image-panel">
  	<div class="image-panel-cell">
		<a href="/img/accelerometer_walking.jpg" ><img src="/img/accelerometer_walking.jpg"  alt="Walking activity"></a>
  	</div>
	<div class="image-panel-cell">
		<a href="/img/accelerometer_jogging.jpg"><img src="/img/accelerometer_jogging.jpg"  alt="Jogging activity"></a>
	</div>
	<div class="image-panel-cell">
		<a href="/img/accelerometer_upstairs.jpg"><img src="/img/accelerometer_upstairs.jpg"  alt="Upstairs activity"></a>
	</div>
	<div class="image-panel-cell">
		<a href="/img/accelerometer_downstairs.jpg"><img src="/img/accelerometer_downstairs.jpg"  alt="Downstairs activity"></a>
	</div>
	<div class="image-panel-cell">
		<a href="/img/accelerometer_standing.jpg"><img src="/img/accelerometer_standing.jpg"  alt="Standing activity"></a>
	</div>
	<div class="image-panel-cell">
		<a href="/img/accelerometer_sitting.jpg"><img src="/img/accelerometer_sitting.jpg"  alt="Sitting activity"></a>
	</div>
</div>


The understanding of these graphics are essential to notice patterns for each activity and then recognize it.
For example we observe repeating waves and peaks for the following repetitive activities walking, jogging, ascending stairs and descending stairs.
We also observe no periodic behavior for more static activities like standing or sitting, but different amplitudes.

## Determine and compute features for the model

Each of these activities demonstrate characteristics that we will use to define the features of the model.
For example, the plot for walking shows a series of high peaks for the y-axis spaced out approximately 0.5 seconds intervals, while it is rather a 0.25 seconds interval for jogging.
We also notice that the range of the y-axis acceleration for jogging is greater than for walking, and so on.
All these observations are very important to determine features we want to use for our model.

We determine a window (a few seconds) on which we will compute all these features.
These features are described below:

<ul>
	<li>Average acceleration (for each axis)</li>
	<li>Standard deviation (for each axis)</li>
	<li>Average absolute difference (for each axis)</li>
	<li>Average resultant acceleration (1/n * sum [√(x² + y² + z²)])</li>
	<li>Average time between peaks (max) (for Y-axis)</li>
</ul>

Now let’s use Einstein to compute all of these features !

<div class="image"><img src="http://127.0.0.1:4000/img/accelerometer_einstein.jpg"  alt="Einstein"></div>

No... Not this one...

## Just few words about Einstein

Einstein is our language which allows to manipulate Geo Time Series and make statistical computations. It is composed by several frameworks and a thousand of functions.

### Bucketize framework

The BUCKETIZE framework provides the tooling for putting the data of a Geo Time Series into regularly spaced buckets.

### Mapper framework

The MAP framework allows you to apply a function on values of a Geo Time Series that fall into a sliding window.

### Reduce framework

The REDUCE framework operates on equivalence classes forming a partition of a set of geo time series.

## Features computation with Einstein

Let’s use Einstein to compute all of these features !

### Average acceleration and Standard deviation


<pre><code class="language-clike">
  $data // call the data

	false // Bessel correction
	MUSIGMA 
	'standev_x' STORE 
	'mean_x' STORE
</code></pre>

### Average absolute difference

<pre><code class="language-clike">
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
</code></pre>

### Average resultant acceleration

<pre><code class="language-clike">
  $data // call the data

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
</code></pre>

### Average time between peaks

<pre><code class="language-clike">
  $data // call the data
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
</code></pre>

## Decision Trees, Random Forest and Multinomial Logistic Regression<

After aggregating all these data, we will use a training data set to create predictive models using classification algorithms (supervised learning). And then we will involve predictions for the activity performing by users.
Here we have chosen the implementation of the *Random Forest*, *Gradient-Boosted Trees* and *Multinomial Logistic Regression* algorithms using MLlib.

Here below the code which shows how to load our dataset, split it into `trainData` and `testData`.


<pre><code class="language-java">
  // Split data into 2 sets : training (60%) and test (40%).
  JavaRDD&lt;LabeledPoint>[] splits = data.randomSplit(new double[]{0.6, 0.4});
  JavaRDD&lt;LabeledPoint> trainingData = splits[0].cache();
  JavaRDD&lt;LabeledPoint> testData = splits[1];
</code></pre>



### Random Forest

Let use the `RandomForest._trainClassifier_` method to fit a random forest model. After that the model is evaluated against the test dataset.

<pre><code class="language-java">
  Map<Integer, Integer> categoricalFeaturesInfo = new HashMap<>();
  int numTrees = 10;
  int numClasses = 4;
  String featureSubsetStrategy = "auto";
  String impurity = "gini";
  int maxDepth = 9;
  int maxBins = 100;

  // create model
  RandomForestModel model = RandomForest.trainClassifier(trainingData, numClasses, 
      categoricalFeaturesInfo, numTrees, featureSubsetStrategy, 
      impurity, maxDepth, maxBins, 12345);

  // Evaluate model on test instances and compute test error
  JavaPairRDD<Double, Double> predictionAndLabel = 
      testData.mapToPair(p -> new Tuple2<Double, Double>(model.predict(p.features()), p.label()));

  // the error
  Double testErr = 
      1.0 * predictionAndLabel.filter(pl -> !pl._1().equals(pl._2())).count() / testData.count();
</code></pre>


### Decision Trees

Let use `DecisionTree._trainClassifier_` to fit a logistic regression multiclass model. After that the model is evaluated against the test dataset.

<pre><code class="language-java">
  Map<Integer, Integer> categoricalFeaturesInfo = new HashMap<>();
  int numClasses = 4;
  String impurity = "gini";
  int maxDepth = 9;
  int maxBins = 100;

  // create model
  final DecisionTreeModel model = DecisionTree.trainClassifier(trainingData, numClasses, 
      categoricalFeaturesInfo, impurity, maxDepth, maxBins);

  // Evaluate model on training instances and compute training error
  JavaPairRDD<Double, Double> predictionAndLabel = 
      testData.mapToPair(p -> new Tuple2<>(model.predict(p.features()), p.label()));

  // the error
  Double testErrDT = 
      1.0 * predictionAndLabel.filter(pl -> !pl._1().equals(pl._2())).count() / testData.count();
</code></pre>

### Multinomial Logistic Regression

Now let use the class `LogisticRegressionWithLBFGS` to fit a logistic regression multiclass model. After that the model is evaluated against the test dataset.

<pre><code class="language-java">
  LogisticRegressionModel model = new LogisticRegressionWithLBFGS()
      .setNumClasses(4)
      .run(trainingData.rdd());

  JavaRDD<Tuple2<Object, Object>> predictionAndLabel = 
      testData.map(p -> new Tuple2<>(model.predict(p.features()), p.label()));

  // Evaluate metrics
  MulticlassMetrics metrics = new MulticlassMetrics(predictionAndLabel.rdd());

  // precision of the model. error = 1 - precision
  Double precision = metrics.precision();
</code></pre>

## Conclusion