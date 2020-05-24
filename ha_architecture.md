# HA Architecture

Everything fails... at some point. So, always plan for failure. 

Topics within HA Architecture (see other md files for details on the topics):

* Load Balancers
  * Application Load Balancers
  * Network Load Balancers
  * Class load balancers
* 504 Error
  * Gateway has timed out - webserver, application layer or DB server layer
* X-Forwarded-For header
* InService vs OutofService re instances monitored by ELB (elastic load balancer)
* Health Checks check the instance health by talking to it.
* Sticky Sessions - users stick to the same EC2 instance
* Cross zone load balancing
* Path patterns to direct traffic to different EC2 instances based on the URL contained in the request
* CloudFormation
  * Quick Start CF templates built by AWS
* Elastic Beanstalk

# Multi-AZ vs Read Replicas for RDS

Multi-AZ: For disaster recovery
Read Replicas for RDS: For performance

For a simulated failure of RDS. You can force a failover from one AZ to another by doing a reboot of the DB. Go up to "actions > reboot". Click "Reboot With Failover?" It will take a website down for a few minutes.

* Can do this with any RDS instance that has multi-AZ configured.

# Scaling out vs Scaling up

## Scaling out

We use auto scaling groups and we add additional EC2 instances.

Horizontal scaling means an app doesn't store previous session info on specific ec2 instances, so any instance can process the request. 

Horizontal scaling is a principle of sound design when it comes to reliability. 

## Scaling up

We increase the resources inside our EC2 instances

* Go from a t2.micro to a 6x extra large.
* An EC2 instance is changed to have more RAM

### Elasticity

The ability to scale computing resources up or down, while *only paying for the resources used*. 

# S3 storage classes

Highly available:

* Standard S3 
  * expensive 
  * availability of 99.99%
  * durability of 99.99999999%
  * For data that has to be frequently updated or accessed in some way
* S3 Standard-IA (Infrequently Accessed)
  * less expensive
  * availability of 99.9% (loss of 0.1% compared to Standard S3)

*Not* highly available: 
* Reduced redundancy storage
* S3 single AZ
  * S3-1Zone-IA only has 99.5% availability



# Availability, Durability, Reliability, Resiliency
Availability - The percent of a time period when the service will be able to respond to your request in some fashion. 
* Takes into account the mean time between failures and the mean time to repair. 
* Refers to the likelihood that you can access a resource or service when you need it.

Resiliency - The ability of a system to self heal after damage or after an event occurs. It will self-recover... but it doesn't mean it will be available continuously during the event.
* A measure of the IT system's ability to self-recover from problems that might otherwise cause it to fail (ie, power outage). 
* The likelihood that a resouce has the ability to recover from damage or disruption
* Increased resiliency is from redundancies and automatic re-routing of operations within a system. 

Reliability - refers to the likelihood that a resource will work as designed
* A measure of how often the IT system fails to operate. 
* Usually measured in terms of the mean time between failures. 
* Closely related to availability, however a system can be "available," but not working properly

Durability - The on-going existence of the object or resource
* The ability of the system to recover committed transaction updates if the system or the storage media fails, for example. 
* If a DB fails, durability refers to recovery of the most recent successful commit. If a disk fails, recovery must be to the most recent successful backup. 

Resources: 
[Quora](https://www.quora.com/What-are-the-differences-between-reliability-availability-resiliency-and-fault-tolerance-in-IT-systems)

# EC2 Placement Groups

Can only be of the following types:
* Cluster
* Spread
* Partition

[AWS' brief description of placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html)

# Example Scenarios

## Example of highly available architecture

User --> Route 53 (with health check --> Regions

Region 1 has:

* An auto scaling group
* public subnets
  * with webserver in each
  * with Nat gateway in each 
* private subnets 
  * with DB servers

Region 2 has:

* An auto scaling group
* public subnets
  * with webserver in each
  * with Nat gateway in each 
* private subnets 
  * with DB servers

Because of all this replication, we have architecture that allows for a region failure or an AZ failure. That way, if one goes down, the other region can pick up the slack, or the other AZ can pick up the slack, depending on the scenario. 

## Example Scenario

Requirement: Website requires a minimum of 6 instances. It must be able to tolerate the failure of 1 AZ. The ideal architecture (while being cost effective) is...

... 3 AZs with 3 instances in each AZ. 

* 9 instances in total is needed, b/c if we lose 1 AZ we'll still have 6 instances to look after the environment. 

## Example Scenario: What can be done to reduce the risk of a single bad deployment taking the entire site down? 

Some Options to consider:

* Multiple autoscaling groups and boundaries. This allows for a staged or 'canary' deployment process.
* Use Route 53, which provides health checks to distribute load across some/numerous ELBs.
* Under each load balancer, use multiple Target groups or auto scaling groups.
* A Classic Load Balancer can be implemented to spread the load over several availability zones.

## Example Scenario: 
A simple single node MySQL RDS instance was created for a pilot project. Now, upgrades need to occur so production can start. We may need to change the size of the instance, depending on how many people use the system during peak periods. We also need to prevent against outages of more than a few seconds during the go-live period. 

Two options to consider:
* Convert the RDS instance to a multi-AZ implementation.
* Consider replacing the RDS instance with [Aurora](https://aws.amazon.com/rds/aurora/) before going live.
  * relatinal db built for the cloud that is MySQL and PostGreSQL compatible.

## Example Scenario:
Create a storage solution for files that will be copies of backups stored on-premise for a company. Need to provide access to these files within milliseconds, if they are needed, but at the same time minimize cost. Availability is not as important as durability.

Good option:
Copy the files to an S3 bucket with the One Zone-IA class. 
* OneZone-IA provides on-line access to files
* Has 11 9's of durability as all the other storage classes
* It's less availability at 99.5%, versus 99.9%
* It's less expensive.

Note that RRS is deprecated! Don't choose this when OneZone-IA is available. 

------------

# Make a HA Architecture Word Press Site
This is a thorough step-through that makes a resiliant, scaleable WordPress site from start to finish.


Tech:
* Route53
* Elastic Load Balancer
* EC2 instances
  * EC2 role
* Auto Scaling Group
* Webserver security group
* RDS security group
* RDS instances - MySQL database
* Multi-AZs
* S3 buckets for redundancy and speed
  * S3 bucket policy 
  * Bucket receives new wordpress blog from an instance and sends to another fleet of instances
* CloudFront to serve images
* Wordpress
  * New articles written by navigating directly to an instance IP address
* Cron
  * Crontab inside /etc directory. Crontab is a way of running commands at after time intervals.
  * Scan S3, check for changes, and if there are changes, download changes to the EC2 instance (chapter 8.9)

> Refer to A Cloud Guru, "AWS Certified Solutions Architect" course for step-by-step instructions on building this via the AWS Console - Chapters 8.7, 8.8, 8.9