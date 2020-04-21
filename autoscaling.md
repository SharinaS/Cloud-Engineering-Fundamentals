# Three Components to Auto Scaling

## (1) Groups
A *logical component* to put your EC2 instances into. 

Webserver group, Application group, Database group, etc.

## (2) Configuration Templates
Groups uses a launch template (launch configuration) for its EC2 instances.

Allows for specification of information
* AMI Id
* Instance type
* KeyPair
* Security groups
* block device mapping
* etc

## (3) Scaling Options
Various options for scaling the Auto Scaling groups

Configure a group to scale based on (5 options):
* a schedule (ie, workers arrive at a certain time in the day). 
* predictive scaling
* demand
* manual control
* maintaining current instance levels all the time.

### Maintain current instance levels all the time:
A specific number of instances can be set to run at all times, within an Auto Scaling group.

EC2 Auto Scaling does a periodic health check on running instances within an Auto Scaling group in order to maintain the desired instance levels. 
* If an unhealthy instance is found, it terminates it and launches a new instance.

### Scale manually:
Most basic form of scaling.

Specify the change in the max, min or desired capacity of your auto scaling group. 

EC2 Auto Scaling manages the process of creating/terminating instances to maintain the updated capacity. 

### Scale based on a schedule:
Helpful to implement when you know the exact times you need to increase/decrease the number of instances in your group, based on the increased/decreased need. 

The scaling actions are done automatically, based on time and date.

### Scale based on demand:
Most popular type of Auto Scaling. 

Uses scaling policies to scale resources - parameters are defined that control the scaling process. 

Super helpful for changeable conditions, but you don't know when the conditions will change. 

Example: You have a web app that runs on 2 instances, and you want the CPU usesage of the Auto Scaling group to be about 50% when the load on the app changes.

### Predictive scaling:
Relatively new.

Use Amazon EC2 Auto Scaling + AWS Auto Scaling to scale resources across multiple services. 

Good for when you want to use predictive and dynamic scaling to maintain ideal availability and performance. 
* proactive
* reactive


# Make One - with the AWS Console
## Make a Configuration
EC2 > Auto Scaling > Launch Configurations

Click "Create launch configuration"

Choose the Amazon Linux 2 AMI, and a t2.micro

Name it.

Choose the IAM role. Choose "S3AdminAccess," in this step-through.

Add a bootstrap script in the Advanced Options:

```
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>Hello World</h1><html>" > index.html
```

Choose whether you want public IP addresses. For this example, choose "Only assign a public IP address to instances launched in the default VPC and subnet (default)"

Click next, and leave storage as default (only need 8 gigs). Click next.

Choose a security group - use your security group created at some other point, which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules.

Click "Review" and Launch configuration (will need to have access to KeyPair).

Note that *nothing has been launched yet*. It's just a configuration setting. 

## Make an Auto Scaling Group
Click on the button on the confirmation page for creating the launch configuration that says, yeah, please make an Auto Scaling group using this launch configuration. 

Make a good group name. 

For Group size, choose 3 (3 instances)

Choose the VPC (default for this example)

Click on the subnets you want. It is intelligent enough to offer up default subnets, all in different AZs.

In Advanced Details, we decide what we want regarding an elastic load balancer. For this example, just leave as default. 

Click Next. We can either keep the group at its initial size, or we can use scaling policies to adjust the capacity of the group. 
* Scale between 3 and 6 instances
* Metric type: Average CPU Utilization
* Target value: 80 <-- as soon as it goes above 80% CPU, start scaling
* Instances need: 60 seconds to warm up after scaling
* Ignore Disable scale-in box.

Click next. Give it some tag names. Click Create.

## Test the Auto Scaling
Go into the instances and terminate 2 out of the 3 instances. Refresh the page... and an instance will automatically {poof} be created to replace the terminated one... and then another one too {poof}... and then a third {poof}. 

Replacements will be put into the appropriate AZs. 

If you max out the CPU, it will scale up the instance numbers to keep up with the CPU needs. 

Check the Activity History inside the Auto Scaling group to see the termination and launching of instances.

-----------

# Resources
* [AWS' What is Amazon EC2 Auto Scaling?](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
* [CloudFormation and AutoScaling](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-as-group.html)