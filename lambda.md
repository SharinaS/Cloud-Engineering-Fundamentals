# Lambda

## Index

* [About](#About)
* [Deployment Configurations](#Deployment-Configurations)
* [IAM](#IAM)
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

## Example

### Meme

* User uploads meme to an S3 bucket
* Lambda triggered
* Lambda takes the meme and metadata and adds words to the picture
* Lambdas can trigger lambdas

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

AWS CodeDeploy can be used as the deployment service.

### Canary

Traffic is shifted in two increments. You can choose from predefined canary options that specify the **percentage of traffic** shifted to your updated Lambda function version in the first increment and the **interval, in minutes**, before the remaining traffic is shifted in the second increment.

### Linear

Traffic is shifted in **equal increments** with an **equal number of minutes** between each increment. You can choose from predefined linear options that specify the percentage of traffic shifted in each increment and the number of minutes between each increment.

### All-at-once

All traffic is shifted from the original Lambda function to the updated Lambda function version at once.
