# Kinesis

[See tutorialdojo CheatSheet](https://tutorialsdojo.com/aws-cheat-sheet-amazon-kinesis/)

AWS > Analytics > Kinesis

A media service and an analytics tool. Easiest way (fully managed service) to reliably load streaming data.

> Amazon Kinesis makes it easy to collect, process, and analyze real-time, streaming data so you can get timely insights and react quickly to new information. Amazon Kinesis offers key capabilities to cost-effectively process streaming data at any scale, along with the flexibility to choose the tools that best suit the requirements of your application. With Amazon Kinesis, you can ingest real-time data such as video, audio, application logs, website clickstreams, and IoT telemetry data for machine learning, analytics, and other applications. Amazon Kinesis enables you to process and analyze data as it arrives and respond instantly instead of having to wait until all your data is collected before the processing can begin. --*AWS*

## Services to load streaming data into

It can capture, transform, and load streaming data into:

* Amazon S3
* Amazon Redshift
* Amazon Elasticsearch Service
* Splunk

--------------

# Kinesis

[Kinesis](#Kinesis)

* [About Kinesis](#About-Kinesis)
* [Streaming Data](#Streaming-Data)
* [Three Types of Kinesis](#Three-Types-of-Kinesis)

# Streaming Data

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

# About Kinesis

A platform on AWS to send streaming data to. 

You can load the data and analyze it, and build custom applications for a business' needs.

# Three Types of Kinesis

## Kinesis Streams

Various devices, EC2, iOT, etc are the data producers.

The data producers stream the data to Kinesis Streams. 

Kinesis Streams stores the data in **shards**

> Kinesis Streams is the only type that has shards! And, data persistence!

* Storage time: 24 hours - 7 days
* Shards: What the data is stored in
  * Each type of data is stored in a different shard
  * Reads: 5 transactions / sec 
  * Read rate: max total data read rate of 2 MB / sec 
  * Writes: Up to 1,000 records / sec 
  * Write rate: max total data write rate of 1 MB / sec (including partition keys)

 Data Consumers: 
 * EC2 instances that the data in the Shards is sent to for analysis (outside of Kinesis Streams). Data is stored in the Shards while analysis is occurring.

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

### Expired Shards

(Sharding is the process of breaking up large tables into smaller chunks called shards that are spread across multiple servers.) 

You have a data analytics application that updates a real-time, foreign exchange dashboard and another separate application that archives data to Amazon Redshift. Both applications are configured to consume data from the same stream concurrently and independently by using Amazon Kinesis Data Streams. However, you noticed that there are a lot of occurrences where **a shard iterator expires** unexpectedly. Upon checking, you found out that **the DynamoDB table used by Kinesis does not have enough capacity** to store the lease data.

Solution: Increase the write table capacity assigned to the shard table.

Explanation: A new shard iterator is returned by every GetRecords request (as `NextShardIterator`), which you then use in the next GetRecords request (as `ShardIterator`). Typically, this shard iterator does not expire before you use it. However, you may find that shard iterators expire because you have not called GetRecords for more than 5 minutes, or because you've performed a restart of your consumer application. If the shard iterator expires immediately before you can use it, this might indicate that the DynamoDB table used by Kinesis does not have enough capacity to store the lease data. This situation is more likely to happen if you have a large number of shards. To solve this problem, increase the write capacity assigned to the shard table.

## Kinesis Firehose

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

## Kinesis Analytics

Works with both Streams and Firehose. 

Can analyze the data on the fly *inside Kinesis* (of either type).

Data is then stored on S3, Redshift or Elastic Search Cluster.