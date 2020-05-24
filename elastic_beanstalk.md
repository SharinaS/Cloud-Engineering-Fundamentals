# Elastic Beanstalk

## PaaS

Elastic Beanstalk is a Platform as a Service (PaaS).

PaaS enables the following for applications on the cloud:

* development
* running
* management 
* no need to build / maintain infrastructure

## About Elastic Beanstalk
Aimed at developers who want to provision a website without having to learn CloudFormation. 

A way of deploying applications to the cloud. AWS will inspect the code and provision the correct services for it. 

Can do auto scaling groups and respond to demand. 

Found in the Compute services. 

Very automated. 

Can configure it to have capacity provisioning. 

Don't have to worry about the infrastructure that runs the application(s).

Elastic beanstalk will *grow* out your infrastructure when you upload your application. 

## Use It 

### On the AWS Console

Click "Get Started"

Give the app a name

Choose a platform - pre-configured, for example. Can use PHP and a Sample application. 

Once environment is deployed, you can change configuration (to the left) and modify instance types, add load balancers, change capacity (single AZ to multi-az), etc.

### See It in One of My Projects

My [step-by-step instructions on deploying to elastic beanstalk](https://github.com/SharinaS/java-fundamentals/wiki/Deploying-to-AWS-with-Elastic-Beanstalk) when dealing with a Java fullstack app (and postgresql db).

