# Index

[Authentication](#Authentication)

[Data Warehousing](#Data-Warehousing)

[ElastiCache](#ElastiCache)

[Non Relational Databases](#Non-Relational-Databases) - NoSQL

* [Compare to RDS](#Compare-to-RDS)
* [DynamoDB](#DynamoDB)
* [DynamoDB Accelerator](#Make-It-Even-Faster)
* [Improve Performance](#Improve-Performance)
* [Types of Reads](#DynamoDB-has-two-types-of-reads)

[Redshift DB](#Redshift)

* [Advanced Compression](#Advanced-Compression)
* [Redshift Use Cases](#Redshift-Use-Cases)

[Relational Databases](#Relational-Databases) - RDS

* [Aurora DB](#Aurora)
  * [Migrate to Aurora](#Migrate-to-Aurora)
* [Backups for RDS](#Backups-for-RDS)
* [Encryption at Rest](#Encryption-at-Rest)
* [Metrics](#Metrics)
  * [Enhanced Monitoring](#Enhanced-Monitoring)
* [Failover](#Failover)
* [Multi AZ](#(1)-Multi-AZ)
* [Read Replicas](#(2)-Read-Replicas)
* [Set up RDS Database](#Set-up-RDS-Database)
* [Two Features of RDS](#Two-Features-of-RDS)

[OLTP vs OLAP](#OLTP-vs-OLAP)

[Write Throughput](#How-to-Increase-Write-Throughput)

# AWS Databases

Certified Solutions Architect exam focuses on:

* RDS
* DynamoDB
* ElastiCache
* Amazon Redshift

## To improve performance...

Use:
 
* read replica(s)
* elasticache

## Storage

When running a DB on an EC2 instance, use EBS for storage.

* Elastic Block Store provides raw block-level storage.
  * It can be attached to an instance
  * Used by RDS

# Relational Databases

Relational databases have been around since the '70s.

## Benefits to RDS

Amazon Relational Database Service (Amazon RDS) makes it easy to set up, operate, and scale a relational database in the cloud. It provides cost-efficient and resizable Compute (and\or Storage) capacity while automating time-consuming administration tasks such as hardware provisioning, operating system maintenance, database setup, patching and backups. It frees you to focus on your applications so you can give them the fast performance, high availability, security and compatibility they need.

Choose RDS because of its resizable compute capacity and its lower administrative burden.

RDS is managed... but not fully-managed, like DynamoDB is.

## Nature of the Queries

RDS uses online transaction processing - [OLTP](#OLTP-vs-OLAP).

## RDS runs on virtual machines

... but you don't get access to those virtual machines.

* As in, *you can't SSH into your RDS instance*. You can't log in to the RDS operating system.
* This comes up in terms of security question and patching/maintenance questions.
  * Patching of the RDS Operating System and DB is Amazon's responsibility.
  * You can't go in and patch the operating system of your RDS instance; that's Amazon's responsibility (though you can do that for the EC2 instance)

## RDS is NOT Serverless

* The exception is Aurora - Aurora Serverless is serverless.

## RDS is a managed database service

Amazon is the one that manages the underlying operating system of the database instance and not you.

Or, circumvent the managed part by deploying, say, your Oracle database to Amazon EC2 instances with data replication between two different Availability Zones.

* HINT: The deployment of this architecture can easily be achieved by using Cloudformation and Quick Start. The Quick Start deploys the Oracle primary database (using the preconfigured, general-purpose starter database from Oracle) on an Amazon EC2 instance in the first Availability Zone. It then sets up a second EC2 instance in a second Availability Zone, copies the primary database to the second instance by using the DUPLICATE command, and configures Oracle Data Guard. --*Udemy AWS Certified Solutions Architect Associate Practice Tests*

## Types of Relational Databases on AWS - 6 in total

There are relational databases, and then there is the service called RDS. 

* SQL Server
* Oracle
* MySQL Server
* PostgreSQL
* Aurora
* MariaDB

## RDS Scales Up and/or Out

You can scale vertically to address the growing demands of an application that uses a roughly equal number of reads and writes. Or you can scale horizontally for read-heavy applications.

> Note that the only RDS database that can scale instances **automatically** is Amazon Aurora. All others require **manual scaling**.

### Scale Vertically

To handle a higher load in your database, you can vertically scale up your master database.  The wide selection of instance types allows you to choose the best resource and cost for your database server. 

There is minimal downtime when you are scaling up on a Multi-AZ environment because the standby database gets upgraded first, then a failover will occur to the newly sized database. 

A Single-AZ instance will be unavailable during the scale operation.

### Scale Horizontally

You can also improve the performance of a read-heavy database by using read replicas to horizontally scale your database. 

Read replicas are not a replacement for the high availability and automatic failover capabilities that Multi-AZ provides.

# Two Features of RDS

* Multi-AZ
* Read replicas

See cool AWS chart for [Multi-AZ vs. Read replicas](https://aws.amazon.com/rds/features/multi-az/).

You can combine Multi-AZ with creating a read replica: 

For example, you can configure a source database as Multi-AZ for high availability and create a read replica (in Single-AZ) for read scalability.

## (1) Multi AZ

***--> Main purpose is disaster recovery and local performance <--***

**Example**: We have a load balancer with 3 EC2 instances behind it. They connect into the RDS DB, all within us-east-1a.

* All changes are synchronously updated from the first DB over to a sister DB over in us-east-1b (you have two copies of your DB, with each copy in a different AZ). 
  * When your production DB is written to, the write will automatically occur to both databases.
  * During DB maintenance, failure, or AZ failure, the RDS will automatically failover to the standby DB.

With Multi-AZ, *The DB endpoint does not change* (no DNS address update required).

Multi-AZ available for all the RDS databases except for Aurora (Aurora by its own architecture is fault-tolerant):

* SQL Server
* Oracle
* MySQL Server
* PostgreSQL
* MariaDB

Multi-AZ deployments for the MySQL, MariaDB, Oracle, and PostgreSQL engines utilize **synchronous** physical replication to keep data on the standby up-to-date with the primary. Multi-AZ deployments for the SQL Server engine use synchronous logical replication to achieve the same result, employing SQL Server-native Mirroring technology.

### Failover

* Failover is automatic with multi-AZ. [See failover section](#Failover).
* If the primary DB fails, the DNS address will automatically point to the secondary DB.
* *You can force a failover from one AZ to another by using "Reboot" of the RDS instance.*

Compare this to a Single-AZ deployment: in case of a Single-AZ database failure, a user-initiated point-in-time-restore operation will be required. This operation can take several hours to complete, and any data updates that occurred after the latest restorable time (typically within the last five minutes) will not be available.

## (2) Read Replicas

***--> Main purpose is scalability <--***

>  Read Replicas provide enhanced performance and durability for database (DB) instances.

You must have *automatic backups* turned *on* in order to deploy a read replica.

### How It Works

When you create a read replica, you first specify an existing DB instance as the source. Then Amazon RDS takes a snapshot of the source instance and creates a read-only instance from the snapshot. 

Amazon RDS then uses the asynchronous replication method for the DB engine to **update the read replica whenever there is a change to the source DB instance.** 

**The read replica operates as a DB instance that allows only read-only connections.** Applications connect to a read replica the same way they do to any DB instance. Amazon RDS replicates all databases in the source DB instance.

### Reduce the load and increase elasticity

You can reduce the load on your source DB instance by routing read queries from your applications to the read replica. 

Using read replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read-heavy database workloads.

Use read replicas for very read-heavy DB workloads.

Every time you do a write to the DB, you'll have a perfect replica that is created, which is the Read Replica.

### Adds Security

public key encryption is used when RDS sets up communication between read replicas and the source DB instance.

### Scaling

Say you have a popular word press site, and you get a huge boost in requests after a particularly popular blog post. You can scale by pointing half of the requests to the primary DB and the other half to read from the read replica. This allows you to scale *out*.

### You can have

* up to 5 copies of Read Replicas of any DB.
* read replicas of read replicas... giving you multiple copies of your production DB... but careful... latency will start increasing.

### Databases

Available for all the following RDS DB's *except for SQL Server*:

* Aurora
* Oracle
* MySQL Server
* PostgreSQL
* MariaDB

Read replicas can be promoted to "Master", to be their *own* DBs

* ... note that this *breaks* the replication, and thus replication will no longer work.

### Aurora read replica

* Under actions for a DB, you can choose to "Create Aurora read replica"
  * This is a good way to migrate a MySQL DB over to Aurora, by creating a replica of the DB.

### DNS Endpoints

* If the primary DB fails, you *must to update your DNS address to point to the Read Replica*, instead of to the primary DB.
* Each read replica will have its own DNS end point.

### Multi-AZ

* *Read replicas can have multi-AZ turned on*, so multiple availability zones are used to house replicas.
* You can create read replicas of Multi-AZ source DBs.

Amazon RDS read replicas can be set up with their own standby instances in a different AZ. 

In the case of Aurora, you can choose to place read replicas across multiple availability zones.

### Regions

Read replicas can be in the same or in different regions.

### Use Cases

Scaling beyond the compute or I/O capacity of a single DB instance for read-heavy database workloads. You can direct this excess read traffic to one or more read replicas.

Serving read traffic while the source DB instance is unavailable. In some cases, your source DB instance might not be able to take I/O requests, for example due to I/O suspension for backups or scheduled maintenance. In these cases, you can direct read traffic to your read replicas. For this use case, keep in mind that the data on the read replica might be "stale" because the source DB instance is unavailable.

Business reporting or data warehousing scenarios where you might want business reporting queries to run against a read replica, rather than your primary, production DB instance.

Implementing disaster recovery. You can promote a read replica to a standalone instance as a disaster recovery solution if the source DB instance fails.

**Example**: You've got an overworked DB with very heavy read traffic. How to increase performance? Add read replicas and point the EC2 instances to those read replicas.

**Example**: You have an elastic load balancer with 4 EC2 instances behind it. They connect to a production DB. The production DB is *asynchronously* replicating to multiple copies. All the EC2 instances are reading/writng to the primary DBs. 

**Example**: You can also architect it so each instance will read from different read replicas, and only write to a single DB. Those writes will then be replicated out. 

## Failover

To deal with infrastructure failure. Part of a Multi-AZ deployment ([go to multi-az section](#(1)-Multi-AZ)).

* Loss of availability in primary Availability Zone
* Loss of network connectivity to primary
* Compute unit failure on primary
* Storage failure on primary

Amazon RDS Multi-AZ deployments do not failover automatically in response to database operations such as long running queries, deadlocks or database corruption errors.

### Standby Replica

In a Multi-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous **standby replica** in a different Availability Zone. 

The primary DB instance is synchronously replicated across Availability Zones to a standby replica to **provide data redundancy, eliminate I/O freezes, and minimize latency spikes during system backups.** 

A Standby Replica is not a scaling solution for read-only scenarios; you cannot use a standby replica to serve read traffic. To service read-only traffic, you should use a [Read Replica - see above](#(2)-Read-Replicas).

> Regardless of the database engine, you cannot use a standby database for read and write operations.

### Failover is Automatic

Failover involves shifting work to the standby replica.

In Amazon RDS, failover is automatically handled -  the endpoint for your DB Instance remains the same - so that you can resume database operations without administrative intervention in the event that your primary database instance went down. 

> When failing over, Amazon RDS simply flips the canonical name record (CNAME) for your DB instance to point at the standby, which is in turn promoted to become the new primary.

* RDS: Failover to the standby
* Aurora: Failover to the read replica

Amazon RDS automatically performs a failover in the event of any of the following:

* Loss of availability in primary Availability Zone
* Loss of network connectivity to primary
* Compute unit failure on primary
* Storage failure on primary

Automatic failover only occurs if the primary database is the one that is affected.

Amazon RDS detects and automatically recovers from the most common failure scenarios for Multi-AZ deployments so that you can resume database operations as quickly as possible without administrative intervention.

[Go to my Disaster Recovery page](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/disaster_recovery.md).

# Backups for RDS

Two types of backups for RDS:

* Automated Backups
* Database Snapshots

## (1) Automated Backups

These are automated, and are done in a scheduled maintenance window.

Let you recover DB to any point in time within a "retention period."  

Retention period:

* 1 - 35 days.

They take a full daily *snapshot*. 

They store *transaction logs* throughout the day.

When you do a recovery, AWS chooses the most recent daily backup, and then applies the transaction logs relevant to that day. 

* Recovery of data can occur to the second, within the retention period :) 

Backups are enabled by *default*.

Backup data is stored in *S3* - you get free storage space equal to the size of your DB

Backups are taken within a defined window (the *backup window*). Storage I/O may be suspended while data is being backed up --> You might experience a little *elevated latency* during the backup.

When you delete the instance, the automated backups are removed too.

## (2) Database Snapshots

Done *manually* ... aka user initiated.

> Creates an entirely new RDS instance.

They are not deleted when you delete the RDS instance (contrast with the automated backups).

## Restoring Backups

For both of the types noted above, the restored version of the DB will be a *new RDS instance with a new DNS endpoint*.

# Encryption at Rest

Supported for all 6 [AWS RDS databases](#Types-of-Relational-Databases)

* MySQL
* Oracle
* MariaDB
* PostgreSQL
* Aurora
* SQL Server

Encryption is done with the **AWS Key Management Service (KMS)**.

> RDS instance is encrypted --> (data stored at rest in the underlying storage is encrypted) +  (automated backups, read replicas & snapshots are encrypted).

# Metrics

## Regular Items from RDS Metrics

Provided by Amazon RDS Metrics in CloudWatch without any extra configuration:

CPU Utilization, Database Connections, and Freeable Memory

## Enhanced Monitoring

> Amazon RDS provides metrics in real time for the **operating system** (OS) that your DB instance runs on. 

You can view the metrics for your DB instance using the console, or consume the Enhanced Monitoring JSON output from CloudWatch Logs in a monitoring system of your choice. 

By default, Enhanced Monitoring metrics are stored in the CloudWatch Logs for 30 days. 

* To modify the amount of time the metrics are stored in the CloudWatch Logs, change the retention for the RDSOSMetrics log group in the CloudWatch console.

### Metrics Provided by Enhanced Monitoring

#### RDS child processes 

Shows a summary of the RDS processes that support the DB instance, for example aurora for Amazon Aurora DB clusters and mysqld for MySQL DB instances. Process threads appear nested beneath the parent process. Process threads show CPU utilization only as other metrics are the same for all threads for the process. The console displays a maximum of 100 processes and threads. The results are a combination of the top CPU consuming and memory consuming processes and threads. If there are more than 50 processes and more than 50 threads, the console displays the top 50 consumers in each category. This display helps you identify which processes are having the greatest impact on performance.

#### RDS processes 

Shows a summary of the resources used by the RDS management agent, diagnostics monitoring processes, and other AWS processes that are required to support RDS DB instances.

#### OS processes

Shows a summary of the kernel and system processes, which generally have minimal impact on performance.

## CPU Utilization Metrics

There are differences between CloudWatch and Enhanced Monitoring Metrics re CPU utilization:

| From CloudWatch | From RDS Enhanced Monitoring |
|-----|-----|
| gathers metrics from the hypervisor for a DB instance | gathers metrics from an agent on the instance |
| the hypervisor layer performs a small amount of work| useful when you want to see how different processes or threads on a DB instance use the CPU|
| ^-- b/c of the work from the hypervisor, you might find differences between the measurements (more so if your DB instances use smaller instance classes) ||
|Does not provide the percentage of the CPU bandwidth and total memory consumed by each database process in your RDS instance|Can check the percentage of the CPU bandwidth and total memory consumed by each process |
| Can monitor the CPU Utilization of your database instance ||

-- *Udemy Certified Solutions Architect Associate Practice Tests*

# How to Increase Write Throughput 

Scenario: You are using a large EC2 Instance with one 500 GB EBS volume to host a relational database.

Solution:

You can achieve this by either setting up a standard **RAID 0** configuration (with 2 EBS volumes) or simply by increasing the **size** of the EC2 instance.

RAID = Redundant Array of Inexpensive Disks

Some EC2 instance types can drive more I/O throughput than what you can provision for a single EBS volume. You can join multiple gp2, io1, st1, or sc1 volumes together in a RAID 0 configuration to use the available bandwidth for these instances. 

RAID 0 is used to increase performance. It will make reliability worse.

* It stripes the data across all the drives. 
* Prevent loss of all data by doing backups.

All RAID is accomplished at the software level. For greater I/O performance than you can achieve with a single volume, RAID 0 can stripe multiple volumes together.

for on-instance redundancy, RAID 1 can mirror two volumes together, so if you lost one drive, you will still retain the data. 

More info in this [YouTube video](https://youtu.be/eE7Bfw9lFfs)

## HVM AMIs

Take note that HVM AMIs are required to take advantage of enhanced networking and GPU processing. In order to pass through instructions to specialized network and GPU devices, the OS needs to be able to have access to the native hardware platform which the HVM virtualization provides.

# Non Relational Databases

Databases that consist of a collection, which is a table. Inside the collection you have documents, which is are rows. 

**Key value pairs** are your fields / columns.

* Allows you to add as many different columns as you like, unlike relational DBs, which require consistency across all the records. This is b/c non-relational DBs are set up like JSON.

Non-relational databases are NoSQL services.

AWS' NoSQL Database is DynamoDB

## DynamoDB

NoSQL DB service.

DynamoDB is serverless with no servers to provision, patch, or manage and no software to install, maintain, or operate. DynamoDB automatically scales tables up and down to adjust for capacity and maintain performance. Availability and fault tolerance are built in, eliminating the need to architect your applications for these capabilities. 

It is a fully managed non-relational database service, scaling and all.

### Overview

* Fast
  * latency is consistent and single-digit at any scale
* Flexible
* Reliable performance
* Fully managed DB
* Supports **key-value** data models
* Stored on SSD storage (this is why it's so fast)
* Spread across **3** geographically distinct data centres (multiple AZs) (redundancy)

### Used for

* mobile
* web
* gaming (see my app, [JavaTag](https://github.com/SharinaS/JavaTag))
* ad-tech
* IoT

### Use Cases

Note that Dynamo is not good for BLOB data - it is too large a chunk of data to be put into a NoSQL database such as DynamoDB.

#### Store metadata for S3 objects

the Amazon DynamoDB stores structured data indexed by primary key and allow low latency read and write access to items ranging from 1 byte up to 400KB. Amazon S3 stores unstructured blobs and is suited for storing large objects up to 5 TB. In order to optimize your costs across AWS services, large objects or infrequently accessed data sets should be stored in Amazon S3, while smaller data elements or file pointers (possibly to Amazon S3 objects) are best saved in Amazon DynamoDB.

To speed up access to relevant data, you can pair Amazon S3 with a search engine such as Amazon CloudSearch or a database such as Amazon DynamoDB or Amazon RDS. In these scenarios, Amazon S3 stores the actual information, and the search engine or database serves as the repository for associated metadata such as the object name, size, keywords, and so on. Metadata in the database can easily be indexed and queried, making it very efficient to locate an object’s reference by using a search engine or a database query. This result can be used to pinpoint and retrieve the object itself from Amazon S3.

#### Managing web sessions

The DynamoDB Time-to-Live (TTL) mechanism enables you to manage web sessions of your application easily. It lets you set a specific timestamp to delete expired items from your tables. Once the timestamp expires, the corresponding item is marked as expired and is subsequently deleted from the table. By using this functionality, you do not have to track expired data and delete it manually. TTL can help you reduce storage usage and reduce the cost of storing data that is no longer relevant.

#### Use with Kinesis to store data

Scenario

You have a data analytics application that updates a real-time, foreign exchange dashboard and another separate application that archives data to Amazon Redshift. Both applications are configured to consume data from the same stream concurrently and independently by using Amazon Kinesis Data Streams. However, you noticed that there are a lot of occurrences where a shard iterator expires unexpectedly. Upon checking, you found out that the DynamoDB table used by Kinesis does not have enough capacity to store the lease data. 

If the shard iterator expires immediately before you can use it, this might indicate that the DynamoDB table used by Kinesis does not have enough capacity to store the lease data.

--> Increase the write capacity assigned to the shard table.

### Compare to RDS

RDS is more suitable for a relational model that normalizes data into tables that are composed of rows and columns. An RDS database enforces referential integrity in relationships between tables, unlike a DynamoDB table.

RDS is just a "managed" service and not "fully managed". This means that you still have to handle the backups and other administrative tasks such as when the automated OS patching will take place.

DynamoDB is a database service in which you no longer need to worry about database management tasks such as hardware or software provisioning, setup and configuration... aka a fully managed database. This means that AWS fully manages all of the database management tasks and the underlying host server. In RDS, you still have to manually scale up your resources and create Read Replicas to improve scalability while in DynamoDB, this is automatically done.

### Storage Amount

Upon sign-up, you'll get 25 GB in the first 12 months under the Free Tier. 

### Make It Even Faster

Amazon DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache that can reduce Amazon DynamoDB response times from milliseconds to **microseconds**, even at millions of requests per second.

### DynamoDB has two types of reads 

> Note the 1 second rule to remember the difference between these.

* Eventual Consistent Reads (Default)
* Strongly Consistent Reads

Eventual Consistent Reads:

* Consistency across all copies of data usually reached within a second.
* Repeating a read after a short time should return the updated data
* Example:
  * An app writes to a DynamoDB table. You want to read the data that was just written. It will take a couple of seconds to get that read.
* Gives "Best Read Performance"

Strongly Consistent Reads:

* Example:
  * An app writes to a DynamoDB table. You want to read the data in <= 1 second.
* Returns a result that reflects all writes that received a successful response prior to the read.

### Improve Performance

**Goal**: Distribute workload evenly and use the provisioned throughput efficiently.

**Solution**: Use partition keys with high-cardinality attributes, which have a large number of distinct values for each item. 

**Explanation**: See this great blog post on AWS about [Partition Keys](https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/)

DynamoDB evenly distributes provisioned throughput but only up to a limit. Reading or writing above the limit can be caused by these issues:

* Uneven distribution of data due to the wrong choice of partition key
* Frequent access of the same key in a partition (the most popular item, also known as a hot key)
* A request rate greater than the provisioned throughput

To avoid request throttling, design your DynamoDB table with the right partition key to meet your access requirements and provide even distribution of data.

**Warning**: Don't mix up partition key with partition group, regarding EC2 instances and placement types!

### DynamoDB Streams

DynamoDB Streams captures a time-ordered sequence of item-level modifications in any DynamoDB table and stores this information in a log for up to 24 hours. Applications can access this log and view the data items as they appeared before and after they were modified, in near-real time.

Amazon DynamoDB is integrated with AWS Lambda so that you can create triggers—pieces of code that automatically respond to events in DynamoDB Streams. With triggers, you can build applications that react to data modifications in DynamoDB tables.

Don't get confused with [Kinesis Streams](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/kinesis.md#Kinesis-Streams)!

### Weakness

If you need to scan large amounts of data (ie a lot of keys all in one query), the performance will not be optimal (Think Redshift, instead). DynamoDB is a NoSQL database which is based on key-value pairs used for **fast processing of small data** that dynamically grows and changes.

### Auto Scaling

DynamoDB auto scaling uses the AWS Application Auto Scaling service to dynamically adjust provisioned throughput capacity on your behalf, in response to actual traffic patterns. This enables a table or a global secondary index to increase its provisioned read and write capacity to handle sudden increases in traffic, without throttling. When the workload decreases, Application Auto Scaling decreases the throughput so that you don't pay for unused provisioned capacity.

#### Scenario

A popular augmented reality (AR) mobile game is heavily using a RESTful API which is hosted in AWS. The API uses Amazon API Gateway and a DynamoDB table with a preconfigured read and write capacity. Based on your systems monitoring, the DynamoDB table begins to throttle requests during high peak loads which causes the slow performance of the game. 

--> Use DynamoDB Auto Scaling

### ALB

An Application Load Balancer is not suitable to be used with DynamoDB 

# Data Warehousing

Used for business intelligence, to pull in large and complex data sets that are usually used by management to do queries on data.

* Queries like, current performance vs targets

Tools include:

* Cognos
* Jaspersoft
* SQL Server Reporting Services
* Oracle Hyperion
* SAP NetWeaver

# OLTP vs OLAP

OLTP = Online Transaction Processing

OLAP = Online Analytics Processing

OLTP differs from OLAP in regards to the **types of queries you run**. This means that the types of queries you need to run are associated with a particular workload... which will help determine what type of database technology you need to choose!

## OLTP Example

OLTP means, say, you have a store, you can look up info about a particular order, using, say, an order number.

* You want to find information about an order, so you run a query for the order number. A row of data will be pulled up, with information about the order. Not too complex.

Choose *provisioned IPS* over standard storage when creating an RDS instance when using online transaction processing in your production environment.

## OLAP Example

OLAP means you are running queries against your DB.

* You are a manager who wants to know the net profit for something. The OLAP transaction will do a large number of queries to pull in a large number of records, and sum up the various categories, it will figure out the sales price versus unit cost, etc.
* Huge amounts of data are returned, after queries sent to various areas.

### Data Warehousing databases

* You use a different database for normal website transactions, and a different database for analytics processing.

## Data Warehousing DBs

Data Warehousing DBs use different types of architecture both from a db perspective and infrastructure layer.

### Redshift

Amazon's Data Warehouse Solution is called **Redshift**.

* Does online analytics processing.

Used for business intelligence or data warehousing.

# Redshift

A way to do business intelligence, or data warehousing in the cloud.

Redshift is a **cloud data warehouse**.

Amazon Redshift is a fast, scalable data warehouse that makes it simple and cost-effective to analyze all your data across your data warehouse and data lake. Redshift delivers ten times faster performance than other data warehouses by using machine learning, massively parallel query execution, and columnar storage on high-performance disk.

* You create a data warehouse with a set of nodes, which are referred to as a *cluster*.

Used for online analytics processing - [OLAP](#OLTP-vs-OLAP)


a Fully-managed, petabyte-scaled data warehouse service.


## Redshift Use Cases

### Queries on tons of data to get reports

Redshift makes it simple and cost effective to run high performance queries on petabytes of structured data so that you can build powerful reports and dashboards using your existing business intelligence tools.

### Get operational insights

Bring together structured data from your data warehouse and semi-structured data such as application logs from your S3 data lake to get real-time operational insights on your applications and systems.

## Cost

* Start at $0.25/hour
  * no commitment or upfront costs.
* Scale up to a petabyte or more for $1000/terabyte/year.
* Cost is less than a 10th of most other data warehousing solutions.

Priced based on *Compute Node Hours*

* Compute Node Hours = the total # of hrs you run across all your compute nodes for the billing period. 
  * Redshift launches a set of nodes called a Redshift cluster
* You are billed for 1 unit / node / hour.
  * Billing is only for *compute nodes, not leader node hours*.

Backup and Data transfer (within a VPC, not outside it) will also be billed.

Example: A 3-node data warehouse cluster running persistently for an entire month (24 hrs x 3 nodes) would incur 2,160 instance hours

## Configurations

* Single node - 160Gb in size, or
* Multi-Node
  * Has a Leader Node - manages client connections and receives queries
  * Has Compute Node(s) - store data and perform queries and computations. Up to *128 Compute Nodes*.

## Advanced Compression

Uses advanced compression.

Instead of compressing data that is stored in rows within a table, it compresses *individual columns*.

* Easier to compress b/c all the data is the same

Multiple compression techniques are used in Redshift, which leads to significant compression. 

Indexes and materialized views are not required, so less space is used compared to tranditional relational DB systems. 

The compression scheme is chosen automatically when you are loading your data into an empty table

## Parallel Processing = Massively Parallel Processing (MPP)

Redshift automatically distributes data and query load across all nodes.

You can add nodes easily, thereby scaling *out*.

## Backups

Backups are enabled *by default* with a 1 day retention period. 

* Max retention period is 35 days (just like RDS)

Redshift always attempts to maintain *>= 3 copies* of your data.

* the original and replica is on the compute nodes, and
* backup in S3

Disaster recovery:

* Asynchronously replicate your snapshots to S3 in another region for disaster recovery

## Security

Data is encrypted

* in transit using SSL
* at rest using AES-256 encryption

Reshift takes care of key managment. You can manage your keys through AWS key management service (KMS) or through a hardware security module (HSM).

## Availability

Only available in one availability zone, so you can't turn on multi-AZ.

### Disaster Recovery

Although Amazon Redshift is a fully-managed data warehouse, you will still need to configure cross-region snapshot copy to ensure that your data is properly replicated to another region.

You can restore snapshots to a new AZ in the event of an outage.

* Redshift can asynchronously replicate your snapshots to S3 in another region for disaster recovery. 

#### Cross-Region Snapshot Copy

You can configure Amazon Redshift to copy snapshots for a cluster to another region. To configure cross-region snapshot copy, you need to enable this copy feature for each cluster and configure where to copy snapshots and how long to keep copied automated snapshots in the destination region. 

When cross-region copy is enabled for a cluster, all new manual and automatic snapshots are copied to the specified region.

## Enhanced VPC routing

When you use Amazon Redshift Enhanced VPC Routing, Amazon Redshift forces all COPY and UNLOAD traffic between your cluster and your data repositories through your Amazon VPC. By using Enhanced VPC Routing, you can use standard VPC features, such as VPC security groups, network access control lists (ACLs), VPC endpoints, VPC endpoint policies, internet gateways, and Domain Name System (DNS) servers. 

Hence, if the problem is that of the requirements is to use **VPC flow logs** to monitor all the COPY and UNLOAD traffic of your Redshift cluster that moves in and out of your VPC. The answer is to enable Enhanced VPC routing on your Amazon Redshift cluster.

You use these features to tightly manage the flow of data between your Amazon Redshift cluster and other resources. When you use Enhanced VPC Routing to route traffic through your VPC, you can also use VPC flow logs to monitor COPY and UNLOAD traffic. 

If Enhanced VPC Routing is not enabled, Amazon Redshift routes traffic through the Internet, including traffic to other services within the AWS network.

# Aurora

## Overview

Amazon's own propritory DB to compete with Oracle and Microsoft's high end DBs.

A **MySQL**- and **PostgreSQL**-compatible relational DB.

It's fast, highly available, but has the simplicity and cost-effectiveness of open source DBs. 

> Performance comparisons:
> * 5x better than MySQL
> * 3x better than PostgreSQL

  ^--- Aurora has a much lower price point than either of these DBs, given these comparisons.

## Storage Autoscaling

Aurora starts at 10Gb.

It scales in 10Gb increments.

It maxes out at **64TB**.

Storage is scaled automatically

## Compute Resources

Scales up to 32vCPUs.

Scales up to 244GB of memory.

## Availability Zones Re Aurora

2 copies of your data is kept in each AZ.

Minimum of 3 AZs are used.

All this gives you at least *6 copies of your data.*

## Scaling and Healing

Aurora can handle the loss of:

* *up to 2* copies of data without affecting DB *write ability*
* *up to 3* copies of data without affecting DB *read ability*

    ^-- can lose a couple of AZs and not notice a thing.

Storage is *self-healing*

* Data blocks and disks are continuously scanned and repaired automatically as needed.

## Aurora Read Replicas

These are read replicas on top of the normal Aurora production DB.

3 different types of Aurora replicas are available:

1. Aurora Replicas (currently can have up to 15)
2. MySQL Read Replicas (currently can have up to 5)
3. PostgreSQL (currently can have up to 1)

Replication type: Asynchronous (milliseconds).

Performance impact on primary - low (vs MySQL is high). 

Replica location - in-region (cannot be cross-region).

Automated failover - yes!

> Automated failover is only available with Aurora Replicas.

Support for user-defined replication delay - none

Support for different data or schema vs primary - no.

In case of an infrastructure failure, Amazon RDS performs an automatic failover to a read replica (or to the standby in the case of a non-Aurora RDS), so that you can resume database operations as soon as the failover is complete. Since the endpoint for your DB Instance remains the same after a failover, your application can resume database operation without the need for manual administrative intervention.

## Aurora Backups

Are always enabled by default. Backups don't impact DB performance.

### Snapshots

Allowed. This does not impact performance of your production DB.

Snapshots can be shared with other AWS accounts.

## Aurora Serverless

An Aurora Serverless DB cluster automatically starts up/down and scales capacity up/down based on your app's needs. [See AWS userguide](https://docs.amazonaws.cn/en_us/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.how-it-works.html).

It's on demand and autoscaling. And "simple!"

Good for workloads that are:

* infrequent
* intermittent
* unpredictable

Example: You want to save as much $ as possible, you don't know when people will access your website, it needs to cope with unpredictable workloads, and it needs to scale.

Serverless lets you pay only when someone is accessing your website (vs by the minute or by the hour).

> With Aurora Serverless , you can create a database endpoint without specifying the DB instance class size. You set the minimum and maximum capacity. With Aurora Serverless, the database endpoint connects to a proxy fleet that routes the workload to a fleet of resources that are automatically scaled. Because of the proxy fleet, connections are continuous as Aurora Serverless scales the resources automatically based on the minimum and maximum capacity specifications. Database client applications don't need to change to use the proxy fleet. Aurora Serverless manages the connections automatically. Scaling is rapid because it uses a pool of "warm" resources that are always ready to service requests. Storage and processing are separate, so you can scale down to zero processing and pay only for storage. --*Udemy AWS Certified Solutions Architect Associate Practice Tests*

Aurora Serverless introduces a new serverless DB engine mode for Aurora DB clusters.

## Aurora Provisioned DB Cluster 

Take note that a non-Serverless DB cluster for Aurora is called a provisioned DB cluster. Aurora Serverless clusters and provisioned clusters both have the same kind of high-capacity, distributed, and highly available storage volume.

When you work with Amazon Aurora without Aurora Serverless (provisioned DB clusters), you can choose your DB instance class size and create Aurora Replicas to increase read throughput. If your workload changes, you can modify the DB instance class size and change the number of Aurora Replicas. This model works well when the database workload is predictable, because you can adjust capacity manually based on the expected workload.

Non-Serverless DB clusters use the provisioned DB engine mode.

## Migration

It takes time to migrate a, say, Oracle database to Aurora. If you want to do this, and it's not an urgent failure sort of situation, you can use AWS Schema Conversion Tool and AWS Database Migration Service. 

See more info about [migration below](#Migrate-to-Aurora)

## Custom Endpoints

Amazon Aurora typically involves a cluster of DB instances instead of a single instance. Each connection is handled by a specific DB instance. When you connect to an Aurora cluster, the host name and port that you specify point to an intermediate handler called an endpoint. Aurora uses the endpoint mechanism to abstract these connections. Thus, you don't have to hardcode all the hostnames or write your own logic for load-balancing and rerouting connections when some DB instances aren't available.

For certain Aurora tasks, different instances or groups of instances perform different roles. For example, the primary instance handles all data definition language (DDL) and data manipulation language (DML) statements. Up to 15 Aurora Replicas handle read-only query traffic.

Using endpoints, you can map each connection to the appropriate instance or group of instances based on your use case. For example, to perform DDL statements you can connect to whichever instance is the primary instance. To perform queries, you can connect to the reader endpoint, with Aurora automatically performing load-balancing among all the Aurora Replicas. For clusters with DB instances of different capacities or configurations, you can connect to custom endpoints associated with different subsets of DB instances. For diagnosis or tuning, you can connect to a specific instance endpoint to examine details about a specific DB instance.

The custom endpoint provides load-balanced database connections based on criteria other than the read-only or read-write capability of the DB instances. For example, you might define a custom endpoint to connect to instances that use a particular AWS instance class or a particular DB parameter group. Then you might tell particular groups of users about this custom endpoint. For example, you might direct internal users to low-capacity instances for report generation or ad hoc (one-time) querying, and direct production traffic to high-capacity instances. 

Hence, creating a custom endpoint in Aurora based on the specified criteria for the production traffic and another custom endpoint to handle the reporting queries is the correct answer.

--*Udemy AWS Certified Solutions Architect Associate Practice Tests*

# ElastiCache

See the [FAQS page here](https://aws.amazon.com/elasticache/faqs/)

> Used to *speed up the performance* of existing databases by caching frequently used identical queries.

A web service that lets you retrieve info from caches

* ... vs disk-based databases (which are slower)
* caches are:
  * fast
  * managed
  * *in-memory*

Lets you deploy, operate and scale an in-memory cache in the cloud to improve the performance of your web app.

## Use Cases

Caching, Session Stores, Gaming, Geospatial Services, Real-Time Analytics, and Queuing.

Example:

* The top 10 products sold on a website can be cached, instead of constantly being pulled from the DB.

Example:

* You go to Amazon and you go to the clothing dept, and you see the top 10 most purchased items. This info is pulled from cached data, instead of from the DB.  

Example:

* Your DB is overloaded. You can add a read replica, and then point your reads to your read replicas. And, you can use elasticache!

## Two caching engines offered by ElastiCache

1. Memcached
2. Redis

These are:
* open-source
* in-memory
* fully managed

### Memcached ("mem-cache-d")

If you want a simple cache to offload your DB, use memcached.

It is able to scale horizontally.

You get multi-threaded performance.

### Redis

Choose this one if you *also* need:

* advanced data types
* ranking / sorting data sets
* publishing / subscribing capabilities
* *multiple AZs*
* persistence
* *backup / restore capabilities*

If you need to authenticate users to allow only certain engineers into the ElastiCache cluster so they can execute Redis commands, use **Redis AUTH**.

* Redis AUTH command can improve data security by requiring the user to enter a password before they are granted permission to execute Redis commands on a password-protected Redis server.

# Authentication

You can authenticate to your DB instance using AWS Identity and Access Management (IAM) database authentication. **IAM database authentication works with MySQL and PostgreSQL**. With this authentication method, you don't need to use a password when you connect to a DB instance. Instead, you use an authentication token.

An authentication token is a unique string of characters that Amazon RDS generates on request. Authentication tokens are generated using AWS Signature Version 4. Each token has a lifetime of 15 minutes. You don't need to store user credentials in the database, because authentication is managed externally using IAM. You can also still use standard database authentication.

IAM database authentication provides the following benefits:

Network traffic to and from the database is encrypted using Secure Sockets Layer (SSL).

You can use IAM to centrally manage access to your database resources, instead of managing access individually on each DB instance.

For applications running on Amazon EC2, you can use profile credentials specific to your EC2 instance to access your database instead of a password, for greater security.

 Note that although you can create and assign an IAM Role to your EC2 instances, you still need to configure your RDS to use IAM DB Authentication.

 -- *Udemy AWS Certified Solutions Architect Associate Practice Tests*

------------
------------

# Set up RDS Database 

> Provision a MySQL DB on the Console with RDS

## Set Up a MySQL DB

AWS > Database > RDS

Click "Create Database" button

Choose "Engine options" - click "MySQL"

Version: Okay to keep at default.

Templates: The "Production" template will give you multi-AZ with a high-performing disk. Choose "Free tier."

Settings:

* Choose a name for the *RDS instance* (this is not exactly the name of the DB).
* Setup a username and a password for the DB.

DB instance size: with free tier, all you get is "Burstable classes." Okay to keep at db.t2.micro.

Storage: Okay to keep at 20 gigs of storage. Even with free-tier, you can have storage autoscaling, which is clicked by default. 

Availability & durability: With free tier, you can't use "Multi-AZ deployment," but this is where you adjust it. 

Connectivity: 

* Choose your VPC, subnet group, and whether it's publicly available. 
* Choose the security group or make a new security group (we'll launch it later if you make a new one).
* Choose a preferred AZ if you want
* Change the DB port if you want, but it's *by default on the MySQL port* at `3306`.

Additional configuration: 

* You must put in an "Initial database name." 
* Backup: In this demo, we don't really want automatic backups turned on, so change "Backup retention period" to `0 days`.
  * If you uncheck the box while keeping the days at some long time, it will continue to do automatic backups, weirdly enough.
* Maintenance: Can choose your maintenance window, for example, every day at 1am, starting on Sunday for 0.5 hours.

Click "Create database"

* It can take a while to create a database - 5-10 minutes. 

The **endpoint** is the way to connect to our database - it is found under the Connectivity tab, and looks a little like: "mydb.ctk2353j49n0s.us-west.2.rds.amazonaws.com"

## Provision a Public EC2 Instance - the Webserver

... if you don't have any.

Make sure that your instance has the following bootstrap script, for this particular demo:

* install php and apache and mysql
* wordpress is unzipped and installed

```bash
#!/bin/bash
yum install httpd php php-mysql -y
cd /var/www/html
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp -r wordpress/* /var/www/html
rm -rf wordpress
rm -rf latest.tar.gz
chmod -R 755 wp-content
chown -R apache:apache wp-content
chkconfig httpd on
service httpd start
```

The security group you assign it to should have the inbound rules of Protocol TCP, for HTTP and SSH, with ports of 80 and 22, respectively... + a second HTTP protocol for ipv6 (::/0).

### Security Groups

The Webserver should be in its own security group, separate from the DB server, since the DB should be in a private subnet.

We need the webserver to speak with the DB server using the MySQL port, so we need to open our DB server inbound rules to receive traffic from the Webserver. 

* Edit DB server inbound rules
* Add rule for MySQL/Aurora, protocol TCP, Port range 3306. 
* For Source, choose the security group for the webserver. 

## Install WordPress into the EC2 instance

Paste the public ip address of the instance into a browser tab. You should see "Welcome to WordPress... blah blah blah".

Fill in the information you created when setting up the database. 

* For Database Host, put in the DB endpoints.

Next, it will open up a new window that says, "Sorry, but I can't write the wp-config.php file." Copy the content. 

SSH into the webserver instsance. Go into the var/www/html folder. Type `nano wp-config.php` file and paste in the content. Save it and exit out. Go back to th browser again and hit "run installation."

Add information in the new window regarding site title and username, etc.

WordPress should be installed.

## Add Multi-AZ deployment

RDS > Databases > DB name > Modify

Under "Multi-AZ deployment," change the radio button from "No" to "Yes" to give us multi availability zones for this database.

Leave everything else as default and click "Continue"

* Note that this change will cause performance impact due to downtime (do during a scheduled maintenance window or after hours). 

Status of DB will change from "available" to "modifying."

Once complete, under the "Configuration" tab below, "Multi AZ" will say, "Yes."

### Reboot

With the DB toggled, go up to "Actions" dropdown menu, and click "Reboot." 

You can also reboot with failover, to force the AZs to change. We won't do this for now. 

### Turn on Backups
Go into your DB by clicking the URL of your DB name, and click the "Modify" button on the upper right. 

Scroll down to "Backup," and in the pull down menu, choose the number of days for your *retention period*, like 35.

Click "Continue"

In the next window, click, "Apply immediately," and then the button, "Modify DB Instance."

The status will change to "modifying"

### Create read replica

You can now go into the pull down menu called "Actions," in the upper right of the list of DBs. Choose "Create read replica" (do a refresh of the screen as needed).

In "Network and Security,"

* Choose the region to have the read replica in.
* Click "No" for "Publicly accessible"

Leave everything else as default unless you'd like multi-AZ deployment (in section "Instance specifications"), which is not the case for this example.

Click button at bottom - "Create read replica"

In the next window, give it a name, which is the "DB instance identifier." Leave all the other stuff as default, and hit "Create."

You should now see two DB's - a Master and a Replica - in the list of DBs.

### Promote read replica - aka, make it another primary DB

This will make the read replica another primary DB. You can then add other read replicas to the new primary DB.

We're not going to do this in this step-by-step guide, though. 

In the list of DBs, go to pull down menu called "Actions," and choose "Promote read replica."

## Migrate to Aurora

Make an Aurora read replica

Given: You have a MySQL DB, with no read replicas.

In the list of DBs, with your DB marked, go up to Actions pulldown and click "Create Aurora read replica" - this will make the MySQL DB and make it into an Aurora read replica.

You can make it in a new AZ or keep it the same.

In the new window, under settings, give it a name under "DB instance identifier."

You can turn on encryption if you want, though in this example I won't. 

You can leave everything as default and click "Create read replica" at the bottom of the page.

In the list of DBs, you can see that there is now an aurora DB, and within it, we have two nodes - a "Writer" node and a "Reader" node.

* The nodes are in separate AZs

When we click on the writer node's url, we can see the DNS "Endpoint." The reader node will have a different DNS endpoint. 

Click on the Writer node's radio button, and go up to "Actions."

* Click "Promote read replica"
  * This will migrate the DB from MySQL to the Aurora DB.
* Can do this with the Writer node too.

If you go to Actions again and click "Take snapshot," you can use that to restore a new Aurora DB using that snapshot.
