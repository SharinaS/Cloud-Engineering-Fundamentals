# Index
[Relational Databases](#Relational-Databases) - RDS

* [Backups for RDS](#Backups-for-RDS)

* [Set up RDS Database](#Set-up-RDS-Database)

* [Encryption at Rest](#Encryption-at-Rest)

* [Multi AZ and Read Replicas](#Two-Features-of-Relational-DBs)

* [Non Relational Databases](#Non-Relational-Databases) - NoSQL

* [DynamoDB](#DynamoDB)

[Data Warehousing](#Data-Warehousing)

[OLTP vs OLAP](#OLTP-vs-OLAP)

[ElastiCache](#ElastiCache)

[Redshift DB](#Redshift)

[Aurora DB](#Aurora)

* [Migrate to Aurora](#Migrate-to-Aurora)

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

RDS uses online transaction processing - [OLTP](#OLTP-vs-OLAP).

RDS runs on virtual machines, but you don't get access to those virtual machines.

* As in, *you can't SSH into your RDS instance*. You can't log in to the RDS operating system.
* This comes up in terms of security question and patching/maintenance questions.
  * Patching of the RDS Operating System and DB is Amazon's responsibility.
  * You can't go in and patch the operating system of your RDS instance; that's Amazon's responsibility (though you can do that for the EC2 instance)

RDS is NOT Serverless

* The exception is Aurora - Aurora Serverless is serverless.

## Types of Relational Databases on AWS - 6 in total

* SQL Server
* Oracle
* MySQL Server
* PostgreSQL
* Aurora
* MariaDB

## Two Features of Relational DBs

### (1) Multi AZ

For *disaster recovery* (not for improving performance).

EC2 instance <---> DB DNS record <---> primary DB + Secondary DB

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

Failover:

* Failover is automatic with multi-AZ. 
* If the primary DB fails, the DNS address will automatically point to the secondary DB.
* *You can force a failover from one AZ to another by using "Reboot" of the RDS instance.*


### (2) Read Replicas

You must have *automatic backups* turned *on* in order to deploy a read replica.

> For performance. *For scaling*. For security.... NOT for disaster recovery. 

Regarding Security:

* public key encryption is used when RDS sets up communication between read replicas and the source DB instance.

Use read replicas for very read-heavy DB workloads.

Read replicas can be in the same or in different regions.

EC2 instance <---> DNS record <----> Primary DB <---> Read Replica

Every time you do a write to the DB, you'll have a perfect replica that is created, which is the Read Replica.

Benefit: Say you have a popular word press site, and you get a huge boost in requests after a particularly popular blog post. You can scale by pointing half of the requests to the primary DB and the other half to read from the read replica. This allows you to scale *out*.

You can have:

* up to 5 copies of Read Replicas of any DB.
* read replicas of read replicas... but careful... latency will start increasing.

**Example**: You have an elastic load balancer with 4 EC2 instances behind it. They connect to a production DB. The production DB is *asynchronously* replicating to multiple copies. All the EC2 instances are reading/writng to the primary DBs. 

**Example**: You can also architect it so each instance will read from different read replicas, and only write to a single DB. Those writes will then be replicated out. 

You can also have read replicas *from* read replicas, giving you multiple copies of your production DB.

* So, read replicas allow you to have a read-only copy of your production DB.
* Do this by using asynchronous replication from the primary RDS instance to the read replica.

Available for all the following RDS DB's *except for SQL Server*:

* Aurora
* Oracle
* MySQL Server
* PostgreSQL
* MariaDB

Read replicas can be promoted to "Master", to be their *own* DBs

* ... note that this *breaks* the replication, and thus replication will no longer work.

Aurora read replica:

* Under actions for a DB, you can choose to "Create Aurora read replica"
  * This is a good way to migrate a MySQL DB over to Aurora, by creating a replica of the DB.

DNS Endpoints

* If the primary DB fails, you *must to update your DNS address to point to the Read Replica*, instead of to the primary DB.
* Each read replica will have its own DNS end point.

Multi-AZ

* *Read replicas can have multi-AZ turned on*, so multiple availability zones are used to house replicas.
* You can create read replicas of Multi-AZ source DBs.

Regions

* You can have a read replica in a second region.
  * As in, you can have a read replica in a region completely different from your primary DB.

**Example**: You've got an overworked DB with very heavy read traffic. How to increase performance? Add read replicas and point the EC2 instances to those read replicas.

## Backups for RDS

Two types of backups for RDS:

* Automated Backups
* Database Snapshots

### (1) Automated Backups

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

### (2) Database Snapshots

Done *manually* ... aka user initiated.

> Creates an entirely new RDS instance.

They are not deleted when you delete the RDS instance (contrast with the automated backups).

### Restoring Backups

For both of the types noted above, the restored version of the DB will be a *new RDS instance with a new DNS endpoint*.

## Encryption at Rest

Supported for all 6 [AWS RDS databases](#Types-of-Relational-Databases)

* MySQL
* Oracle
* MariaDB
* PostgreSQL
* Aurora
* SQL Server

Encryption is done with the **AWS Key Management Service (KMS)**.

RDS instance is encrypted --> (data stored at rest in the underlying storage is encrypted) +  (automated backups, read replicas & snapshots are encrypted).


# Non Relational Databases

Databases that consist of a collection, which is a table. Inside the collection you have documents, which is are rows. Key value pairs are your fields, or columns.

Allows you to add as many different columns as you like, unlike relational DBs, which require consistency across all the records. This is b/c non-relational DBs are set up like JSON.

Non-relational databases are NoSQL services.

AWS NoSQL Database:

* DynamoDB

## DynamoDB

NoSQL DB service.

Overview:

* Fast
  * latency is consistent and single-digit at any scale
* Flexible
* Reliable performance
* Fully managed DB
* Supports key-value data models
* Stored on SSD storage (this is why it's so fast)
* Spread across 3 geographically distinct data centres (multiple AZs) (redundancy)

Used for:

* mobile
* web
* gaming (see my app, [JavaTag](https://github.com/SharinaS/JavaTag))
* ad-tech
* IoT
* etc

### Storage Amount

Upon sign-up, you'll get 25 GB in the first 12 months under the Free Tier. 

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

OLTP differs from OLAP in regards to the types of queries you run.

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

* You create a data warehouse with a set of nodes, which are referred to as a *cluster*.


Used for online analytics processing - [OLAP](#OLTP-vs-OLAP)

## Overview

* fast
* powerful
* fully managed
* petabyte-scale
* data warehouse service - *the* one to use in the AWS world.

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

You can restore snapshots to a new AZ in the event of an outage.

* Redshift can asynchronously replicate your snapshots to S3 in another region for disaster recovery. 

# Aurora

## Overview

Amazon's own propritory DB to compete with Oracle and Microsoft's high end DBs.

A **MySQL**- and **PostgreSQL**-compatible relational DB.

It's fast, highly available, but has the simplicity and cost-effectiveness of open source DBs. 

Performance comparisons:

* 5x better than MySQL
* 3x better than PostgreSQL

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

* Automated failover is only available with Aurora Replicas.

Support for user-defined replication delay - none

Support for different data or schema vs primary - no.

## Aurora Backups

Are always enabled by default. Backups don't impact DB performance.

### Snapshots

Allowed. This does not impact performance of your production DB.

Snapshots can be shared with other AWS accounts.

## Amazon Aurora Serverless

An Aurora Serverless DB cluster automatically starts up/down and scales capacity up/down based on your app's needs. 

It's on demand and autoscaling. And "simple!"

Good for workloads that are:

* infrequent
* intermittent
* unpredictable

Example: You want to save as much $ as possible, you don't know when people will access your website, it needs to cope with unpredictable workloads, and it needs to scale.

Serverless lets you pay only when someone is accessing your website (vs by the minute or by the hour). 

# ElastiCache

> Used to *speed up the performance* of existing databases by caching frequently used identical queries.

A web service that lets you retrieve info from caches

* ... vs disk-based databases (which are slower)
* caches are:
  * fast
  * managed
  * *in-memory*

Lets you deploy, operate and scale an in-memory cache in the cloud to improve the performance of your web app.

Example:

* The top 10 products sold on a website can be cached, instead of constantly being pulled from the DB.

Example:

* You go to Amazon and you go to the clothing dept, and you see the top 10 most purchased items. This info is pulled from cached data, instead of from the DB.  

Example:

* Your DB is overloaded. You can add a read replica, and then point your reads to your read replicas. And, you can use elasticache!

## Two caching engines supported by ElastiCache

* These are:
  * open-source
  * in-memory

1. Memcached
2. Redis

### Memcached ("mem-cache-d")

If you want a simple cache to offload your DB, use memcached.

It is able to scale horizontally.

You get multi-threaded performance.

... and that's pretty much it.

### Redis

Choose this one if you *also* need:

* advanced data types
* ranking / sorting data sets
* publishing / subscribing capabilities
* *multiple AZs*
* persistence
* *backup / restore capabilities*

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
