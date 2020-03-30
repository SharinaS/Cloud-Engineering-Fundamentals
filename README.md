# Cloud Engineering Fundamentals - AWS
A repository dedicated to the learning of cloud engineering, specifically AWS Services. This project started after earning my certificate in "Advanced Software Development in Java" at Code Fellows in Seattle, and as such also includes links to notes I took during that academic time period. 

Do note that a number of these technologies, such as Amplify, have changed versions since I did projects using them, so, check the docs carefully when applicable, and switch to the most up to date version. The amplify docs are a great example - some of my notes may still reference amplify 1.0; at the time of writing this, you actually want to make sure you link over to amplify 2.0 version of the docs, otherwise I can just about guarantee your project won't run properly (yes, that's experience talking, unfortunately). 

## Certifications
Launched initially in 2013. There are now 10 certifications available. 

# Overview

### Availability Zone
Essentially an availability zone is a data center. A data center is filled with "computer stuff" - inside are servers, switches, firewalls, load balancers, storage, etc. If many data centers are close together, they are counted as one availability zone. 

### Region
Composed of two or more availability zones. A region is a geographical area. 

### Edge Locations
Endpoints for AWS which are used for caching content. There are always more edge locations than regions. Edge locations typically consist of CloudFront (Amazon's Content Delivery Network / CDN).

# Amplify 
### Notes
You may find my notes on integrating Amplify with Android, along with a number of AWS services, Amplify and Android in my [Java Fundamentals wiki](https://github.com/SharinaS/java-fundamentals/wiki). Specific topics associated with Amplify and Android - with links to this wiki - are found below:
* [Amplify and Android](https://github.com/SharinaS/java-fundamentals/wiki/Android-AWS-Amplify-and-Android)
* [API](https://github.com/SharinaS/java-fundamentals/wiki/Android-AWS-Amplify-and-Database-Setup-with-GraphQL-API)
* [Authentication](https://github.com/SharinaS/java-fundamentals/wiki/Android-Authentication)
* [Cognito](https://github.com/SharinaS/java-fundamentals/wiki/Android-AWS-Cognito)
* [Database and GraphQL](https://github.com/SharinaS/java-fundamentals/wiki/Android-AWS-Amplify-and-Database-Setup-with-GraphQL-API)
* [Kinesis and Pinpoint](https://github.com/SharinaS/java-fundamentals/wiki/Android-Amazon-PinPoint-and-Kinesis,-and-Experiments), and more on [Pinpoint](https://github.com/SharinaS/java-fundamentals/wiki/Android-Notifications)
* [Location Services](https://github.com/SharinaS/java-fundamentals/wiki/Android-Location)
* [Subscriptions](https://github.com/SharinaS/java-fundamentals/wiki/Android-Subscriptions)
* [S3 and File Uploads](https://github.com/SharinaS/java-fundamentals/wiki/Android-Amplify-S3-and-File-Uploads)

### Projects
[GFTastyNoms is an Android App](https://github.com/SharinaS/GFTastyNoms) I'm working on to further explore Amplify, AWS and Android. The Readme has notes about the various AWS services used in the app.

[JavaTag, aka ZombieTag, is an Android App](https://github.com/SharinaS/JavaTag) built during a sprint cycle with a team of five other developers. It uses Amplify and various AWS services.

[Taskmaster is an Android app](https://github.com/SharinaS/taskmaster) I built as a learning project during my time at Code Fellows. It has a range of AWS services, integrated in with Amplify. FYI, it uses version 1.0 of Amplify, which is now out of date. 

# CloudWatch
To get automatic notifications if account bill gets too high, you go into CloudWatch to create a billing alarm. The billing alarm uses an SNS topic (a way of emailing you whenever the threshold is reached).

# EC2
### Notes 
[Use the Amazon console and your local terminal](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md), from my repository file titled EC2.md.

[Build a simple web server](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md) in my EC2 file. 

# Elastic Beanstalk
### Notes
My [step-by-step instructions on deploying to elastic beanstalk](https://github.com/SharinaS/java-fundamentals/wiki/Deploying-to-AWS-with-Elastic-Beanstalk) when dealing with a Java fullstack app (and postgresql db).

### Projects
My [full-stack Java app - Music Central](https://github.com/SharinaS/music-central) - makes use of several AWS services. The readme has notes from my experience of integrating these services into the app.

# IAM
* IAM = Identify And Access Management
* Security controls regarding access to AWS resources.

### Notes 
My Java 401 class notes on [IAM in my wiki](https://github.com/SharinaS/java-fundamentals/wiki/IAM---AWS-Identify-and-Access-Management) from my time at Code Fellows in Seattle. 

[My evolving notes on roles, policies and other IAM topics](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/IAM.md)

### Resources
* [1Strategy March blog post on IAM](https://www.1strategy.com/blog/2020/03/10/ten-tips-for-improved-iam-security/)

# Load Balancer
### My Notes
[Info on load balancers](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/LOADBALANCER.md)

### My Projects
[Music Central - a fullstack Java app](https://github.com/SharinaS/music-central) - has a load balancer.

# Route 53
Change where the domain name points, such as when adding a load balancer to an instance that already has Route 53 setup - [see my notes on this in this repo](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/LOADBALANCER.md).

See my notes when I set up Route 53 for my app, Music Central - scroll down in [the readme](https://github.com/SharinaS/music-central/blob/master/README.md).

# S3
### Notes
[This repo's S3.md file](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/S3.md) has notes about using the terminal to interact with S3 via the terminal.

# SageMaker
Provides ability to build, train and deploy machine learning models. A fully managed services to remove the grunt work of machine learning. Provides all the components used for machine larning in one toolset. 

### Resources
[AWS](https://aws.amazon.com/sagemaker/)

### Projects
My [full-stack Java app - Music Central](https://github.com/SharinaS/music-central) - makes use of several AWS services. The readme has notes from my experience of integrating these services into the app.

