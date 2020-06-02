# ECS

## Index
 
[Docker and Batch Jobs](#Docker-and-Batch-Jobs)

[Fargate](#Fargate)

[Launch Types](#Launch-Types)

## About ECS

ECS = Elastic Container Service

> Amazon Elastic Container Service (ECS) is **used to run containerized applications** either using Amazon EC2 (server-based) or AWS Fargate (serverless).

* In contrast, **store** Docker container images in the AWS Cloud with ECR - [See below](#ECR).

Amazon Elastic Container Service (Amazon ECS) is a highly scalable, fast, container management service that makes it easy to run, stop, and manage Docker containers on a cluster.

[About ECS on AWS](https://aws.amazon.com/ecs/)

## Fargate

AWS Fargate is serverless compute for containers.

Fargate makes it easy for you to focus on building your applications. Fargate removes the need to provision and manage servers, lets you specify and pay for resources per application, and improves security through application isolation by design.

Fargate allocates the right amount of compute, eliminating the need to choose instances and scale cluster capacity. 

### Billing

You only pay for the resources required to run your containers, so there is no over-provisioning and paying for additional servers.

### Workload Isolation

Fargate runs each task or pod in its own kernel providing the tasks and pods their own isolated compute environment. This enables your application to have workload isolation and improved security by design. This is why customers such as Vanguard, Accenture, Foursquare, and Ancestry have chosen to run their mission critical applications on Fargate.

### Serverless Compute

Fargate is a **serverless compute engine** for containers that work with both Amazon Elastic Container Service (ECS) and Amazon Elastic Kubernetes Service (EKS)

## Launch Types

* Fargate
* EC2

### Fargate Launch Type

You can host your cluster on a serverless infrastructure that is managed by Amazon ECS by launching your services or tasks using the Fargate launch type. 

### EC2 Launch Type

For more control you can host your tasks on a **cluster** of Amazon Elastic Compute Cloud (Amazon EC2) instances that you manage by using the EC2 launch type.

## Elastic Beanstalk vs ECS

AWS Elastic Beanstalk can also be used to rapidly develop, test, and deploy Docker containers in conjunction with other components of your application infrastructure. 

However, using Amazon ECS directly provides more fine-grained control and access to a wider set of use cases.

## What are Containers

A Docker container is a standardized unit of software development, containing everything that your software application needs to run: code, runtime, system tools, system libraries, etc. 

Containers are created from a read-only template called an image.

## Can be Used With:

IAM

EC2 Auto Scaling

Elastic Load Balancing

Elastic Container Registry (ECR)

CloudFormation

## Regional

Amazon ECS is a regional service that simplifies running application containers in a highly available manner across **multiple Availability Zones** within a Region. 

## VPC

You can create Amazon ECS clusters within a new or existing VPC. After a cluster is up and running, you can define task definitions and services that specify which Docker container images to run across your clusters.

## ECR

Amazon Elastic Container Registry (ECR) is a fully-managed Docker container registry that makes it easy for developers to **store, manage, and deploy** Docker container **images**. 

Amazon ECR is integrated with Amazon Elastic Container Service (ECS), simplifying your development to production workflow. 

Amazon ECR eliminates the need to operate your own container repositories or worry about scaling the underlying infrastructure.

## Docker and Batch Jobs

Docker containers are particularly suited for batch job workloads. Batch jobs are often short-lived and embarrassingly parallel. You can package your batch processing application into a Docker image so that you can deploy it anywhere, such as in an Amazon ECS task.

Amazon ECS supports batch jobs. You can use Amazon ECS Run Task action to run one or more tasks once. The Run Task action starts the ECS task on an instance that meets the task’s requirements including CPU, memory, and ports.

### Example

For example, you can set up an ECS Batch architecture for an image processing application. You can set up an AWS CloudFormation template that creates an Amazon S3 bucket, an Amazon SQS queue, an Amazon CloudWatch alarm, an ECS cluster, and an ECS task definition. Objects uploaded to the input S3 bucket trigger an event that sends object details to the SQS queue. The ECS task deploys a Docker container that reads from that queue, parses the message containing the object name and then downloads the object. Once transformed it will upload the objects to the S3 output bucket.

### Use SQS to provide scaling

By using the SQS queue (---^)as the location for all object details, you can take advantage of its scalability and reliability as the queue will automatically scale based on the incoming messages and message retention can be configured. The ECS Cluster will then be able to scale services up or down based on the number of messages in the queue.

### IAM

You have to create an IAM Role that the ECS task assumes in order to get access to the S3 buckets and SQS queue. Note that the permissions of the IAM role don't specify the S3 bucket ARN for the incoming bucket. This is to avoid a circular dependency issue in the CloudFormation template. You should always make sure to assign the least amount of privileges needed to an IAM role, such as `taskRoleArn`.

## Use Cases

* Batch jobs
* Microservices

More about use cases in the [AWS Developer Guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/common_use_cases.html)