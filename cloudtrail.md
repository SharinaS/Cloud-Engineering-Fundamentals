# CloudTrail

> AWS CloudTrail is an AWS service that can be used to monitor all user interactions with the AWS environment.

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

### CloudTrail and S3

By default, CloudTrail records bucket-level events. To get logs for object-level operations like GetObject, DeleteObject, and PutObject, you must configure object-level logging. Object-level logging incurs additional charges