# AWS Applications Index

[Elastic Transcoder](#Elastic-Transcoder)

[Kinesis](#Kinesis)

* [About Kinesis](#About-Kinesis)
* [Streaming Data](#Streaming-Data)
* [Three Types of Kinesis](#Three-Types-of-Kinesis)

[SES](#SES)


[SWF](#SWF)

* [About SWF](#About-SWF)
* [SQS vs SWF](#SQS-vs-SWF)

[Web Identity Federation and Cognito](#Web-Identity-Federation-and-Cognito)

* [User Pools](#User-Pools)

-------
-------
# SES

SES = Simple Email Service

Amazon SES (Amazon Simple Email Service) is a flexible, affordable, and highly-scalable email messaging platform for businesses and developers.

# SWF

SWF = Simple Work Flow

> A fully-managed state tracker and task coordinator service

Think of SWF if a question refers to human workers and a need to manage that work.

## About SWF

A way of coordinating work across distributed application components... or a way to coordinate work across multiple components of your application.

SWF is a way of coordinating both your applications as well with human beings / manual processes

Task coordination use cases:

* media processing
* web application backends
* busines process workflows
* analysis piopeslines

Tasks are invocations of various processing steps in an application, which are preformed by:

* executable code
* web service calls
* human actions
* scripts

A way of combining the digital environment with human beings.

In SWF, you've gotten a human element to factor into the design process. If there's any sort of manual process, you will be using SWF.

## SWF Actors

### Workflow Starters

An app that can initiate a workflow.

Ie, 

* an e-commere website following the placement of an order
* mobile app searching for bus times

### Deciders

This controls the flow of activity tasks in a workflow execution.

If something has finished / failed in a workflow, a Decider chooses what to do next.

### Activity Workers

These carry out the activity tasks

### SWF Domain

A collection of related workflows.

## SQS vs SWF

The Thing|SQS|SWF|
|-----|-----|-----|
|Timing|Retention period up to 14 days| workflow executions up to 1 year|
|API|message-oriented|*task*-oriented|
|Duplication|Need to handle duplicated messages (default type)|A task is assigned only once and is never duplicated|
|Tracking|Need to implement your own app-level tracking (esp w/multiple queues)|Keeps track of all tasks and events in an app|


# Elastic Transcoder

A media transcoder in the cloud

A way to *convert media files* from their original source format into different formats that will play on smartphones, tablets, PCs, etc.

A way of transcoding video.

Has *presets for popular output formats*, meaning you don't need to guess about which settings work best on particular devices.

## Fees

Pay based on

* the minutes you transcode
* The resolution at which you transcode

## Example

* Upload your video to S3
* A lambda function is triggered
* Lambda function looks at all the metadata and sends the video over to Elastic transcoder
* Transcoder looks at the video and transcodes it to look nice on a wide variety of devices.
* Transcoder ships the transcoded videos to another S3 bucket

# Kinesis

## Streaming Data

Data that is generated continuously from thousands of data sources.

* Data sources send in the data records simultaneously, in small sizes (in kilobytes)

Examples:

* Purchases from online stores
  * unique item id, and other transactional data
* Stock prices
* Game data (as the gamer plays)
  * level achieved
* Social network data
* Geospatial data
  * Where you are on the map
  * Traffic
* iOT sensor data
  * sensors used on a farm

## About Kinesis

A platform on AWS to send streaming data to. 

You can load the data and analyze it, and build custom applications for a business' needs.

## Three Types of Kinesis

### Kinesis Streams

Various devices, EC2, iOT, etc are the data producers.

The data producers stream the data to Kinesis Streams. 

Kinesis Streams stores the data in *shards*

> Kinesis Streams is the only type that has shards! And, data persistence!

* Storage time: 24 hours - 7 days
* Shards: What the data is stored in
  * Each type of data is stored in a different shard
  * Reads: 5 transactions / sec 
  * Read rate: max total data read rate of 2 MB / sec 
  * Writes: Up to 1,000 records / sec 
  * Write rate: max total data write rate of 1 MB / sec (including partition keys)

 Data Consumers: EC2 instances that the data in the Shards is sent to for analysis (outside of Kinesis Streams). Data is stored in the Shards while analysis is occurring.

After analysis, the data is sent from the consumers to a variety of places that the EC2 instances can access

* DynamoDB
* S3
* EMR
* Redshift
* RDS

Key components of Kinesis Streams:

* Producers
* Shards
* Consumers

Data Capacity of the Stream

* A function of the number of shards that you specify for the stream.
* Total capacity = sum of the capacities of the shards

### Kinesis Firehose

Various devices, EC2, iOT, etc are the data producers.

The data producers stream the data to Kinesis Firehose.

There is *no persistant storage* inside K. Firehose. The data has to be analyzed time of!

* Can have lambda functions inside the firehose. 
  * When the data comes in, the lambda is triggered, and the function runs a particular block of code for that data.

Data is then outputted from Firehose.

* It can be outputted directly to S3
* It can be outputted to Redshift via S3
* Elastic search cluster

The easiest way to load streaming data into data stores and analytics tools

* Can capture, transform and load streaming data into S3, Redshift, Elasticsearch Service, Splunk.

Key components of Kinesis Firehose:

* Delivery streams
* Records of data and destinations

### Kinesis Analytics

Works with both Streams and Firehose. 

Can analyze the data on the fly *inside Kinesis* (of either type).

Data is then stored on S3, Redshift or Elastic Search Cluster.

# Web Identity Federation and Cognito

## Web Identity Federation

Your users are allowed to gain access to AWS resources after they authenticate with a **Web Identity Provider** (Amazon, FB, Google). 

The user receives an authentication code after successful authentication from the **Web ID provider**. They can then trade that code for temporary security credentials.

Ultimately just lets you sign into a website using your FB or Google credentials, etc. 

## Cognito 

> A Web IDentity Federation service

Allows you to sign-in / sign-up to your apps

Allows access for guest users

Cognito acts as an **Identity Broker** between your app and *Web ID providers*, so you don't need to write extra code.

Synchronizes user data from multiple devices

* If you change the username on one device, it will trickle out to your other devices.

Recommended for all mobile applications with AWS services.

### Recommended Approach

Say you have a website with:

* Lambda
* DynamoDB
* S3

Series of events:

* User will authenticate with FB (for example). 
* FB gives the user an authentication token
* User sends auth token to cognito
* Cognito responds and grants access to the AWS env't (depending on the access yout permit)
* User can start lambda functions, store data in the DB or store images in S3, etc

### Benefits of Cognito

* No need for the app to imbed or store AWS credentials on the device
* Users get a seamless experience across all mobile devices

### User Pools

> Takes care of the user side of authentication - usernames, passwords, registration

They are directories that manage sign-up and sign-in functionality for mobile and web apps.

Users can sign-in directly to the User Pool - username/password is stored within Cognito itself.

Or, users can sign-in using FB, Amazon or Google - third party.

* Cognito acts as an *Identity Broker* between the identity provider and AWS. 
* Successful authentication generates a JSON Web token (JWT). 

### Identity Pools

> Takes care of authorizing access to AWS resources - the actual granting of authentication

Provide *temporary* AWS credentials to access AWS services like S3 or DynamoDB.

### Step-by-Step

* User logs in using FB account
* FB authenticates her and passes back an authentication token to Cognito.
* Cognito converts that token to a JWT
* User sends the JWT to an Identity Pool
* Identity Pool grants the user credentials in the form of an IAM role
* User can access AWS resources

### Cognito Synchronisation

Cognito uses Push Synchronization to push updates and synchronize user data across multiple devices.

Uses *SNS* to send a notification to all devices associated with a given user identity whenever data stored in the cloud changes.
