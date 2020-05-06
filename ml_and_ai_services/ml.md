# Machine Learning

Index:

[Amazon Forecast](#Amazon-Forecast)

[Amazon Athena](#Amazon-Athena)

[Amazon Personalize](#Amazon-Personalize)

Applications

* analyzing images
* natural language processing
* forecasts
* anaomaly detection
* clustering

Challenges

* Not enough data
* non-representative data
* poor quality data
* too many features (bogs the model down)
* overfitting/underfitting
  * overfitting is when your model resembles your testing data set so closely, it can' predict the real world. 
  * underfitting is when you model doesn't fit the model closely enough. 

# Amazon Forecast

Forecasting concepts:

* Granularity - the distance of time between the collection of data points. 
* Losing resolution - 
* Horizon - the time period you want to predict for. You want the data collection to occur as close to the horizon as possible.

Amazon forecast can improve forecasting accuracy by up to 50% at 1/10th the cost.

## Example architecture

CSV --> S3-1 --> Forecast --> S3-2 --> QuickSight

Sagemaker --> Forecast

S3-1 --> Glue

# Predictors

* ARMIA
* DeepAR+
* ETS
* NPTS - predicts future value of distribution by taking past observation
* Prophet - an additive regression model

# Clustering

Inputs of a certain type will always result in a category A output, and inputs of another type will always result in a category B output. This allows us to cater to different groups of customers.

You can use the product to see which ones are similar, or you can see how demographics are similar so you can recommend products. 

You can track that a person likes flamingos and donut-shaped things, and recommend a flamingo-shaped pool floaty. 

# Amazon Personalize

It produces private, customized personalization API. 

Amazon personalize takes care of loading data, inspecting data, identifying features, selecting algorithms, selecting hyperparameters, training models, optimizing models, buiding feature store, deploying and hosting models, creating real-time caches. 

You can build real-time predictions for what a customer wants, unlike Amazon Forecast.

It allows you to monitor what things a customer clicks on (ie, in Amazon), and gets more specific suggestions for the customer. 

### Emails

One of the services of this product is to send out tailored emails to recipients. 

# Amazon Athena

An interactive query service that makes it easy to analyze data in S3 using standard SQL. 

So, it looks like you can query a csv file of data, via Athena...

# Resources

1Strategy AI
