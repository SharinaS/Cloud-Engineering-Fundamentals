# ECS

ECS = Elastic Container Service

Amazon Elastic Container Service (ECS) is **used to run containerized applications** either using Amazon EC2 (server-based) or AWS Fargate (serverless).

* In contrast, **store** Docker container images in the AWS Cloud with ECR - [See below](#ECR).

[About ECS on AWS](https://aws.amazon.com/ecs/)

AWS Fargate is serverless compute for containers.

* Fargate is a serverless compute engine for containers that work with both Amazon Elastic Container Service (ECS) and Amazon Elastic Kubernetes Service (EKS)

Amazon Elastic Container Service (Amazon ECS) is a highly scalable, fast, container management service that makes it easy to run, stop, and manage Docker containers on a cluster.

You can host your cluster on a serverless infrastructure that is managed by Amazon ECS by launching your services or tasks using the Fargate launch type. 

For more control you can host your tasks on a **cluster** of Amazon Elastic Compute Cloud (Amazon EC2) instances that you manage by using the EC2 launch type.

## Elastic Beanstalk vs ECS

AWS Elastic Beanstalk can also be used to rapidly develop, test, and deploy Docker containers in conjunction with other components of your application infrastructure; however, using Amazon ECS directly provides more fine-grained control and access to a wider set of use cases.

## Regional

Amazon ECS is a regional service that simplifies running application containers in a highly available manner across multiple Availability Zones within a Region. 

You can create Amazon ECS clusters within a new or existing VPC. After a cluster is up and running, you can define task definitions and services that specify which Docker container images to run across your clusters.

## What are Containers

A Docker container is a standardized unit of software development, containing everything that your software application needs to run: code, runtime, system tools, system libraries, etc. Containers are created from a read-only template called an image.

## Can be Used With:

IAM

EC2 Auto Scaling

Elastic Load Balancing

Elastic Container Registry (ECR)

CloudFormation

## ECR

Amazon Elastic Container Registry (ECR) is a fully-managed Docker container registry that makes it easy for developers to store, manage, and deploy Docker container images. Amazon ECR is integrated with Amazon Elastic Container Service (ECS), simplifying your development to production workflow. Amazon ECR eliminates the need to operate your own container repositories or worry about scaling the underlying infrastructure.