# AutoScaling

## Index 

* [3 Components to Auto Scaling](#3-Components-to-Auto-Scaling)
* [Cooldown Period](#Cooldown-Period)
* [Launch Configuration](#(2)-Configuration-Templates)
* [Scaling Options](#(3)-Scaling-Options)
* [Order of Termination](#Order-of-Termination)
* [Services that Scale Automatically](#Services-that-Scale-Automatically)

# 3 Components to Auto Scaling

## (1) Groups

A *logical component* to put your EC2 instances into. 

Webserver group, Application group, Database group, etc.

## (2) Configuration Templates

Whenever you create an Auto Scaling group, you must specify a launch configuration, a launch template, or an EC2 instance. When you create an Auto Scaling group using an EC2 instance, Amazon EC2 Auto Scaling automatically creates a launch configuration for you and associates it with the Auto Scaling group.

Specify information for the instances in the launch template or launch configuration:

* AMI Id
* Instance type
* KeyPair
* Security groups
* block device mapping
* etc

### Launch Configurations

You can only specify one launch configuration for an Auto Scaling group at a time, and you can't modify a launch configuration after you've created it. 

Therefore, if you want to change the launch configuration for an Auto Scaling group, you must create a launch configuration and then update your Auto Scaling group with the new launch configuration.

If you plan to continue to use launch configurations with Amazon EC2 Auto Scaling, be aware that not all Auto Scaling group features are available. For example, you cannot create an Auto Scaling group that launches both Spot and On-Demand Instances or that specifies multiple instance types.

### Launch Templates

A launch template is similar to a launch configuration, in that it specifies instance configuration information. 

However, defining a launch template instead of a launch configuration allows you to have **multiple versions of a template**. 

With versioning, you can create a subset of the full set of parameters and then reuse it to create other templates or template versions. For example, you can create a default template that defines common configuration parameters and allow the other parameters to be specified as part of another version of the same template.

Launch templates provide more advanced Amazon EC2 configuration options. For example, you must use launch templates to use Amazon EC2 Dedicated Hosts.

### Replace Launch Configuration with a Launch Template

After you replace the launch configuration for an Auto Scaling group, any new instances are launched using the new launch template, but existing instances are not affected. In this situation, you can terminate existing instances in the Auto Scaling group to force a new instance to launch that uses your launch template. Or, you can allow automatic scaling to gradually replace older instances with newer instances based on your termination policies.

## (3) Scaling Options

Various options for scaling the Auto Scaling groups

Configure a group to scale based on (5 options):

* a schedule (ie, workers arrive at a certain time in the day)
* predictive scaling
* demand
* manual control
* maintaining current instance levels all the time.

### Maintain current instance levels all the time:

A specific number of instances can be set to run at all times, within an Auto Scaling group.

EC2 Auto Scaling does a periodic health check on running instances within an Auto Scaling group in order to maintain the desired instance levels.

* If an unhealthy instance is found, it terminates it and launches a new instance.

### Scale manually

Most basic form of scaling.

Specify the change in the max, min or desired capacity of your auto scaling group. 

EC2 Auto Scaling manages the process of creating/terminating instances to maintain the updated capacity. 

### Scale based on a schedule

Helpful to implement when you know the exact times you need to increase/decrease the number of instances in your group, based on the increased/decreased need. 

The scaling actions are done automatically, based on time and date.

### Scale based on demand

Most popular type of Auto Scaling. 

Uses scaling policies to scale resources - parameters are defined that control the scaling process. 

Super helpful for changeable conditions, but you don't know when the conditions will change. 

Example: You have a web app that runs on 2 instances, and you want the CPU usesage of the Auto Scaling group to be about 50% when the load on the app changes.

### Predictive scaling

Relatively new.

Use Amazon EC2 Auto Scaling + AWS Auto Scaling to scale resources across multiple services. 

Good for when you want to use predictive and dynamic scaling to maintain ideal availability and performance.

* proactive
* reactive

# Order of Termination

The default termination policy is designed to help ensure that your network architecture spans Availability Zones evenly. With the default termination policy, the behavior of the Auto Scaling group is as follows:

1. If there are instances in multiple Availability Zones, choose the Availability Zone with the most instances and at least one instance that is not protected from scale in. If there is more than one Availability Zone with this number of instances, choose the Availability Zone with the instances that use the oldest launch configuration.

2. Determine which unprotected instances in the selected Availability Zone use the oldest launch configuration. If there is one such instance, terminate it.

3. If there are multiple instances to terminate based on the above criteria, determine which unprotected instances are closest to the next billing hour. (This helps you maximize the use of your EC2 instances and manage your Amazon EC2 usage costs.) If there is one such instance, terminate it.

4. If there is more than one unprotected instance closest to the next billing hour, choose one of these instances at random.

# Cooldown Period

Summary

* It ensures that the Auto Scaling group does not launch or terminate additional EC2 instances before the previous scaling activity takes effect.
* Its default value is 300 seconds.
* It is a configurable setting for your Auto Scaling group.

The cooldown period is a configurable setting for your Auto Scaling group that helps to ensure that it doesn't launch or terminate additional instances before the previous scaling activity takes effect. After the Auto Scaling group dynamically scales using a simple scaling policy, it waits for the cooldown period to complete before resuming scaling activities.

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

# Services that Scale Automatically

Amazon S3 and Amazon EFS are storage services that scale automatically in storage capacity without any intervention to meet increased demand.

Also, AWS Lambda dynamically scales function execution in response to increased traffic.

(EMR *does not* not scale automatically.*

# Resources
* [AWS' What is Amazon EC2 Auto Scaling?](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
* [CloudFormation and AutoScaling](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-as-group.html)