# Lambda

## Index

* [About](#About)
* [Connecting a Lambda to a VPC](#Connecting-a-Lambda-to-a-VPC)
* [Deployment Configurations](#Deployment-Configurations)
* [IAM](#IAM)
* [Metrics](#Metrics)
* [Pricing](#Pricing)
* [Scaling](#Scaling)
* [Triggers](#Lambda-Triggers)
* [Use Cases](#Use-Cases)

## About

When building applications on AWS Lambda the core components are Lambda functions and triggers. A trigger is the AWS service or application that invokes a function, and a Lambda function is the code and runtime that process events. 

A compute service where you upload your code and create a lambda function. 

AWS Lambda takes care of:

* provisioning and managing the servers that you use to run the code.

No need to worry about:

* operating systems
* patching
* scaling
* servers
* anti-virus
* EC2 instances crashing

Lambda functions can trigger other lambda functions. 

* 1 event can equal x number functions, if functions trigger other functions. 
* 1 event could trigger 20 different lambda functions.

Lambda can do things globally.

* You can use it to back up S3 buckets over to other S3 buckets, for example. 

## IAM

Lambda needs to have a Role associated with it that provide credentials with rights to other services.

## Use Cases

See also [AWS' list of suggestions](https://docs.aws.amazon.com/lambda/latest/dg/applications-usecases.html)

### Event driven compute service

AWS Lambda runs your code in response to events. These events could be changes to data in an S3 bucket or a DynamoDB table.

These event driven events are called *triggers*.

### Compute service

Run your code in response to HTTP requests using API Gateway or API calls made using SDKs.

* "SDKs take the complexity out of coding by providing language-specific APIs for AWS services" - AWS

### Memes

* User uploads meme to an S3 bucket
* Lambda triggered
* Lambda takes the meme and metadata and adds words to the picture
* Lambdas can trigger lambdas

### Images Sent to Kinesis then S3

There is an internal application that serves as a repository for images uploaded by a couple of users. Whenever a user uploads an image, it is sent to Kinesis for processing before it is stored in an S3 bucket. Afterwards, if the upload is successful, the application will return a prompt telling the user that the upload is successful. The entire processing typically takes about 5 minutes to finish.

To asynchronously process the request to the application in the most cost-effective manner, create a Lambda function that will asynchronously process the requests. 

Note that using Step Functions with Lambda is excessive in this situation and is not cost-effective: AWS Step Functions service lets you coordinate multiple AWS services into serverless workflows so you can build and update apps quickly. Although this can be a valid solution, it is not cost-effective since the application does not have a lot of components to orchestrate. Lambda functions can effectively meet the requirements in this scenario without using Step Functions. This service is not as cost-effective as Lambda.

## Scaling

If you have high numbers of users accessing an API Gateway, a high number of lambda functions will be used to deal with the jobs.

* Lambda functions are independent -- *1 event = 1 function*

Lambda is continuously scaling!

* Every time you make an invocation to an API Gateway, it will trigger *a separate Lambda function*!

Lambda scales *out* (not up) automatically.

## Threading

Lambda (and EC2 and ECS) support hyper-threading on one more more virtual CPUs. 

## Languages for Lambda

* Node.js
* Java
* Python
* C#
* Go
* PowerShell

## Pricing

Based on two categories

### Number of requests

* Less than 1 mill requests: free
* Greater than 1 mill requests: $0.20 / million requests

Very cheap compared to EC2 - hundreds versus hundreds of thousands of dollars per month.

### Duration

Based on the length of time the function runs.

* From start of execution till it returns or terminates.
* Rounds up to the nearest 100ms.

Price depends on the amount of *memory* allocated to your function.

* $0.00001667 / GB-second used

## Alexa

Everytime you speak with Alexa, you are directly talking to Lambda.

## Lambda Triggers

### What can't trigger lambda

Very important in the exam to know what *can't* trigger lambda.

* RDS

### Triggers for Lambda

Direct (Synchronous) Triggers - functions execute immediately when you perform the Lambda Invoke API call:

* Elastic Load Balancing (Application Load Balancer)
* Cognito
* Lex
* Alexa
* API Gateway
* CloudFront (Lambda@Edge)
* Kinesis Data Firehose

Asynchronous Triggers / Invokes - asynchronous invokes place your invoke request in Lambda service queue and AWS processes the requests as they arrive. 

* S3
* SNS
* Simple Email Service
* Cloudformation
* CloudWatch Logs and Events
* CodeCommit
* Config

Poll-Based Triggers (Invokes) - lambda will poll the following services on your behalf, retrieve records, and invoke your functions:

* Kinesis
* SQS
* DynamoDB Streams

See the AWS docs about this topic [here](https://aws.amazon.com/blogs/architecture/understanding-the-different-ways-to-invoke-lambda-functions/)

## Deployment Configurations

If you're using the AWS Lambda compute platform, you must choose one of the following deployment configuration types to specify how traffic is shifted from the original AWS Lambda function version to the new AWS Lambda function version.

* Canary
* Linear
* All-at-once

AWS CodeDeploy can be used as the deployment service.

### Canary

Traffic is shifted in two increments. You can choose from predefined canary options that specify the **percentage of traffic** shifted to your updated Lambda function version in the first increment and the **interval, in minutes**, before the remaining traffic is shifted in the second increment.

### Linear

Traffic is shifted in **equal increments** with an **equal number of minutes** between each increment. You can choose from predefined linear options that specify the percentage of traffic shifted in each increment and the number of minutes between each increment.

### All-at-once

All traffic is shifted from the original Lambda function to the updated Lambda function version at once.

## Connecting a Lambda to a VPC

You can configure a function to connect to a virtual private cloud (VPC) in your account. Use Amazon Virtual Private Cloud (Amazon VPC) to create a private network for resources such as databases, cache instances, or internal services. 

Connect your function to the VPC to access private resources during execution.

AWS Lambda runs your function code securely within a VPC by default. 

However, to enable your Lambda function to access resources inside your private VPC, you must provide additional VPC-specific configuration information that includes VPC **subnet IDs and security group IDs**. 

AWS Lambda uses this information to set up elastic network interfaces (ENIs) that enable your function to connect securely to other resources within your private VPC.

Lambda functions cannot connect directly to a VPC with dedicated instance tenancy (Your instance runs on single-tenant hardware). To connect to resources in a dedicated VPC, peer it to a second VPC with default tenancy.

Your Lambda function automatically scales based on the number of events it processes. If your Lambda function accesses a VPC, you must make sure that your VPC has sufficient ENI capacity to support the scale requirements of your Lambda function. It is also recommended that you specify at least one subnet in each Availability Zone in your Lambda function configuration.

By specifying subnets in each of the Availability Zones, your Lambda function can run in another Availability Zone if one goes down or runs out of IP addresses. If your VPC does not have sufficient ENIs or subnet IPs, your Lambda function will not scale as requests increase, and you will see an increase in invocation errors with EC2 error types like `EC2ThrottledException`. For asynchronous invocation, if you see an increase in errors without corresponding CloudWatch Logs, invoke the Lambda function synchronously in the console to get the error responses.

### Reasons for EC2ThrottledException

* You only specified one subnet in your Lambda function configuration. That single subnet runs out of available IP addresses and there is no other subnet or Availability Zone which can handle the peak load.

* Your VPC does not have sufficient subnet ENIs or subnet IPs.

## Metrics

AWS Lambda automatically monitors functions on your behalf, reporting metrics through Amazon CloudWatch. These metrics include total **invocation** requests, **latency**, and **error rates**. The *throttles*, **Dead Letter** Queues errors and **Iterator** age for stream-based invocations are also monitored.

You can monitor metrics for Lambda and view logs by using the Lambda console, the CloudWatch console, the AWS CLI, or the CloudWatch API.

