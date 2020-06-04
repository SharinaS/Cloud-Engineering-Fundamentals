# Application Auto Scaling 

AKA Dynamic Scaling

## Index

* [About](#About)
* [Scaling Types](#Scaling-Types)

## About

Application Auto Scaling is a web service for developers and system administrators who need a solution for automatically scaling their scalable resources for individual AWS services beyond Amazon EC2. Application Auto Scaling allows you to configure automatic scaling for the following resources:

Amazon ECS services

Spot Fleet requests

Amazon EMR clusters

AppStream 2.0 fleets

DynamoDB tables and global secondary indexes

Aurora replicas

Amazon SageMaker endpoint variants

Custom resources provided by your own applications or services. For more information, see the GitHub repository.

Amazon Comprehend document classification endpoints

Lambda function provisioned concurrency

Amazon Keyspaces (for Apache Cassandra) tables

You can also use Application Auto Scaling and Amazon EC2 Auto Scaling in combination with AWS Auto Scaling to scale resources across multiple services. AWS Auto Scaling can help you maintain optimal availability and performance by combining predictive scaling and dynamic scaling (proactive and reactive approaches, respectively) together to scale your Amazon EC2 capacity faster.

## Scaling Types

Application Auto Scaling allows you to automatically scale your scalable resources according to conditions that you define.

### Target tracking scaling

Scale a resource based on a target value for a specific CloudWatch metric.

With target tracking scaling policies, you choose a scaling metric and set a target value. Application Auto Scaling creates and manages the CloudWatch alarms that trigger the scaling policy and calculates the scaling adjustment based on the metric and the target value. The scaling policy adds or removes capacity as required to keep the metric at, or close to, the specified target value. In addition to keeping the metric close to the target value, a target tracking scaling policy also adjusts to changes in the metric due to a changing load pattern.

Target tracking scaling policies are not supported for Amazon EMR.

### Step scaling

Scale a resource based on a set of scaling adjustments that vary based on the size of the alarm breach.

With step scaling, you choose scaling metrics and threshold values for the CloudWatch alarms that trigger the scaling process as well as define how your scalable target should be scaled when a threshold is in breach for a specified number of evaluation periods.

When step adjustments are applied, and they increase or decrease the current capacity of your Auto Scaling group, the **adjustments vary based on the size of the alarm breach**.

#### Compare to Simple Scaling

The main issue with simple scaling is that after a scaling activity is started, the policy must wait for the scaling activity or health check replacement to complete and the cooldown period to expire before responding to additional alarms. Cooldown periods help to prevent the initiation of additional scaling activities before the effects of previous activities are visible.

In contrast, with step scaling the policy can continue to respond to additional alarms, even while a scaling activity or health check replacement is in progress. Therefore, all alarms that are breached are evaluated by Amazon EC2 Auto Scaling as it receives the alarm messages.

#### Compare to Target Tracking

Target tracking scaling policy increases or decreases the current capacity of the group based on a target value for a specific metric, instead of a set of scaling adjustments.

#### Limitations

Step scaling policies are not supported for DynamoDB, Amazon Comprehend, Lambda, or Amazon Keyspaces (for Apache Cassandra).

#### Scenario

An application is hosted in an Auto Scaling group of EC2 instances. To improve the monitoring process, you have to configure the current capacity to increase or decrease based on a set of scaling adjustments. This should be done by specifying the scaling metrics and threshold values for the CloudWatch alarms that trigger the scaling process.

Which of the following is the most suitable type of scaling policy that you should use?

--> Step scaling

### Scheduled scaling

Scale a resource based on the date and time.

To use scheduled scaling, create scheduled actions, which tell Application Auto Scaling to perform scaling activities at specific times. When you create a scheduled action, you specify the scalable target, when the scaling activity should occur, and the minimum and maximum capacity. At the specified time, Application Auto Scaling scales based on the new capacity values.