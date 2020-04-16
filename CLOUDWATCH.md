# About CloudWatch
A monitoring service for:
* AWS resources
* Applications run on AWS

Cloudwatch monitors performance for:
* Compute
  * EC2 Instances
  * Autoscaling groups
  * Elastic load balancers
  * Route53 health checks
* Storage & Content Delivery
  * EBS Volumes
  * Storage Gateways
  * CloudFront

CloudWatch with EC2 will by default monitor events *every 5 minutes* 
* You can have 1 minute intervals by turning on *detailed monitoring*.

### CloudWatch & EC2
CloudWatch can monitor at a host level. This includes metrics such as:
* CPU
* Network
* Disk
* Status Check
  * hypervisor
  * EC2 instance

### Dashboards:
In CloudWatch, you can create dashboards. A dashboard can be created to either show us our region, another region, or even the entire global overview. Essentially, a dashboard shows what is happening in the AWS environment. 

### Alarms:
Lets you set alarms to notify you when particular thresholds (ie, billing, cpu usage) are hit.

### Logs:
CloudWatch > Logs 
Allows for performance logging. So, you can send all your logs to CloudWatch. Allows for aggregation, monitoring and storing of logs.

### Events:
CloudWatch Events deliver nearly a real-time stream of system events that describe changes in AWS resources. This lets you respond to state changes.

## CloudTrail vs. CloudWatch
Don't get confused! AWS will try to trip you up on an exam about these two services.

CloudTrail records AWS Management Console actions and monitors API calls within the AWS platform.
* Leads to increase in visibility into your user and resource activity.
* Allows for identifying:
  * which users and accounts called AWS
  * source IP address from which the calls were made
  * when the calls were made

So, anytime you make an EC2 instance or an S3 bucket, CloudTrail is making notes. 
* "Who provisioned the EC2 instance?" <-- think CloudTrail

While CloudWatch is about *performance*, CloudTrail is about *auditing*.
* "What's the performance of the EC2 instance?" <-- think CloudWatch

# Do Cool Things with CloudWatch

### Make the instance with CloudWatch turned on:
Launch a new instance. Amazon Linux 2 AMI, t2.micro. 

In "Configure Instance Details," go down to "Monitoring." Click the radio button that says, "Enable CloudWatch detailed monitoring."
* Checks every minute.

Okay to use default for the "Add Storage" step.

Add some tags

Add to an existing security group - ... use a self-created security group (which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules).

Launch. 

### Examine the new instance:
Under the monitoring tab, can see host-level metrics, like the CPU utilization, our disk, network and status checks. We can't see RAM utilization or how much disk storage space is left <-- to do that we need custom metrics, by the way. 

### Test it:
*Goal: Log into the EC2 instance and max out the CPU {evil laugh}.*

Set up an alert that tells us when the CPU utilization goes above a certain percentage:
* Make sure you know what your instance Id is. 
* Go into Management and Governance > CloudWatch
* Click on "Alarms" in the vertical nav bar. 
* Click the blue button, "Create new alarm" 
* Click the button in the middle of the grey square that says "Select metric"
* In the tab "All metrics" down below, click on "EC2," then "Per-Instance Metrics"
* Click the radio button of the InstanceID that is yours, with a "Metric Name" of "CPUUtilization," and click the blue button, "Select metric."
* Midway down the new window, you'll see "Alarm details." Give the alarm a name and a description. Set your CPUUtilization amount you want to be alerted about in "Whenever: CPUUtilization." 
  * The number you choose after giving it a conditional, is a percentage. Ie, >= 90%.
  * The "for 1 out of __ datapoints" means, "for 1 minute out of a minute, for CPUUtilization of greater than 90%, it will send an alarm. 
  * You can edit it to say alternatively other things, like "for 2 minutes out of 5," etc.
* Down scroll down to "Actions," and select who to send the notification to and click "Create Alarm."
  * Click "New list" and add an appropriate email address.

In CloudWatch Alarms, if it says "INSUFFICIENT_DATA," don't be alarmed, it is probably because you are not yet using your instance. 

Go to your EC2 instances, and copy the public IP address of the instance. 

SSH into the instance. After elevating privlages to root, write the following to put the ec2 instance into *an infinite loop* {mwahaha!}:

```
[root@ip-111-11-11-111 ec2-user]# while true; do echo; done
``` 
You'll see a bright blue flashy box... and then you'll get an emailed alarm! In CloudWatch you'll see ALARM is triggered and is bright red.