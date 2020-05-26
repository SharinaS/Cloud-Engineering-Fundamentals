# Billing


# About AWS Billing

[AWS Whitepaper on Pricing](https://d1.awsstatic.com/whitepapers/aws_pricing_overview.pdf?did=wp_card&trk=wp_card) - comes up about 30% for Cloud Practitioner Exam

Pay as you go.

* Reduce costs by 70% or more compared to using services for a 24-hr period

Pay less when you reserve

Pay even less per unit by using more resources

Pay even less as AWS grows

Custom pricing. 

## Three Fundamental Drivers of Cost

Compute

Storage

Data *outbound*

* data-in is (nearly?) always free-of-charge

# Avoid Extra Charges by Deleting Certain Things

Once the user's work is completed she should:

1- Delete all Elastic Load Balancers.

2- Terminate all unused EC2 instances.

3- Delete the attached EBS volumes that he doesn’t need.

4- Release any unused Elastic IPs.

## Becareful of Auto-Restarts

Some services automatically restart resources after terminating them without notifying you, and as a result, you get unexpected charges on your bill.

Examples of these services:

1- Elastic Beanstalk:

Elastic Beanstalk is designed to ensure that all the resources that you need are running, which means that it automatically relaunches any service that you stop. If you need to permanently delete those resources you must terminate your Elastic Beanstalk environment before you terminate resources that Elastic Beanstalk has created.

2- AWS OpsWorks:

If you use the AWS OpsWorks environment to create AWS resources, you must use AWS OpsWorks to terminate those resources or AWS OpsWorks will restart them. For example, if you use AWS OpsWorks to create an Amazon EC2 instance, but then stop it by using the Amazon EC2 console, the AWS OpsWorks auto-healing feature categorizes the instance as failed and restarts it.

# Billing
The following includes services and the factors that lead to an increased bill for each service.

## EC2

Clock Hours of Server Time

Instance Type

Pricing Model

* On Demand
* Reserved
* Spot
* Dedicated hosts (physical EC2 server)

Number of Instances

Load Balancing

Detailed Monitoring 

* Standard monitoring is every 5 min. Detailed monitoring is every minute.

AutoScaling 

* Can increase number of instances used

Elastic IP Addresses

Operating Systems and Software Packages (linux less expensive than Windows)

## Lambda

Number of requests

* Free tier - 1 mill requests per month
* after free tier - 0.2 / mill requests 

Duration Pricing - how long the function occurs

* There's upt to 3.2 mill seconds of compute time free

Additional charges

* From using other services - ie, reading/writing to EC2

## EBS

Volumes (per GB)

Snapshots (per GB)

Data Transfer

* outbound charges are tiered (inbound is free)

## S3

Storage Class (ie, Standard vs IA)

Storage - how much data

Number of requests

* GET has different rates than PUT and COPY

Data transfer

* Outbound data costs $; inbound free

## Glacier

Storage 

Data retrieval times

* 1-5 min
* 3-5 hrs
* 5-12 hours

## Snowball

Service fee per job

* 50TB - $200
* 80TB - 250

Daily Charge

* First 10 days free, then $15/day

Data Transfer 

* out of S3 is billed; *in* is *free*;

## RDS

Clock hours of server time

What type of DB you have 

* characteristics like engine, size, memory class

DB purchase type - the different instance types (how large the DB is)

Number of DB instances

Provisioned storage - how big in gigs

* Charges start when the DB instance is *terminated* - there's no charge for backup storage up to 100% for an active DB instance.

Additional storage after provisioned storage.

Number of requests - input and output requests

Deployment type - single AZ vs multiple AZs and # of AZs

Data transfer

* out is tiered; *in* is *free* 

## DynamoDB

Provisioned throughput (read)

Provisioned throughput (write)

Data storage - hourly rate per GB of disk space

## CloudFront

Traffic distribution

* Pricing based on where the edge locations are.

Number and type of requests (HTTP or HTTPS) and geographic location where those requests are made.

Data transfers *out*.

# Organizations

Allows for either option:

* Consolidated Billing
* All Features

Can apply policies to an OU (organizational unit) preventing them from doing anything but serverless, for example. Policies will trickle down into the AWS accounts that are under the OU. 

Can also apply policies directly to an AWS Account that are inside an OU.

## Consolidated Billing

With Consolidated Billing, the Paying Account *cannot make changes* to any of the resources owned by a Linked Account.

The maximum number of Linked Accounts per Paying Account under Consolidated Billing is **20** (unless you request more)


# Capex vs Opex

## Capex = Capital Expenditure

You pay up front. It's a fixed, sunk cost. 

## Opex = Operational Expenditure

Pay for what you use.

Like paying utility bills.

# Support Plans

Basic

* freeeeee

Developer

* Starts at $29/month

Business

* Starts at $100/month
* Production system down - < 1 hr

Enterprise

* Starts at $15k/month
* Production system down - < 1 hr
* Technical Account Manager provided

# Resource Groups and Tags

## Tags

Tagging lets you find all your resources with various tags attached to them.

Tagging is a global service.

Tags are key-value pairs attached to AWS resources, and have metadata (data about data).

## Resource Groups

Lets you group your resources using the tags assigned to them. You can group resources that share one or more tags.

A resource group is useful for describing and reporting on resources 

* for grouping identification
* for internal cost recovery.

Resource groups contain info like:

* Region
* Name
* Employee ID
* Etc
* EC2 public / private IP addresses
* ELB port configurations

Groups is on a per region basis (not global).

Can use Resource groups + AWS Systems manager to allow you to control and execute automation, ie, against fleets of EC2 instances.

# Landing Zone

Deployed into an Organizations account. Lets folks set up multi-account environment based on AWS best practices.

Contrast with: QuickStart - lets you deploy environments quickly using pre-built CF templates.





