# Global Accelerator

## Index

* [Compare with CloudFront](#Compare-with-CloudFront)
* [Use Cases](#Use-Cases)

## About

See also the [tutorialsdojo notes](https://tutorialsdojo.com/aws-global-accelerator/).

A service in which you create accelerators to improve availability and performance of your app for local and global users. 

Global Accelerator directs traffic to optimal endpoints over the AWS global (backbone) network. 

AWS Global Accelerator is a service that **improves the availability and performance** of your applications with local or global users. 

AWS Global Accelerator uses the AWS global network to optimize the path from your users to your applications, improving the performance of your traffic by as much as 60%.

## Low Latency
Many applications, such as gaming, media, mobile applications, and financial applications, need very low latency for a great user experience. To improve the user experience, AWS Global Accelerator directs user traffic to the nearest application endpoint to the client, thus reducing internet latency and jitter. It routes the traffic to the closest edge location via Anycast, then by routing it to the closest regional endpoint over the AWS global network. AWS Global Accelerator quickly reacts to changes in network performance to improve your users’ application performance.

## Multiple Regions

 While ELB provides load balancing within one Region, AWS Global Accelerator provides traffic management across multiple Regions.

AWS Global Accelerator complements ELB by extending these capabilities beyond a single AWS Region, allowing you to provision a global interface for your applications in any number of Regions.

## Components to a Global Accelerator

### Static IP addresses

Global Accelerator provides static IP addresses that act as a **fixed entry point to your application endpoints** in a single or multiple AWS Regions, such as your Application Load Balancers, Network Load Balancers or Amazon EC2 instances.

GA gives you *two static IP addresses* that you associate with your accelerator. You can also bring your own IP addresses.

### Accelerator

The resource you create to direct traffic to optimal endpoints over the AWS global network.

### DNS name (supplied with set up of accelerator)

  * will point to the static IP addresses that GA assigns to you. 
  * can use your accelerator's static IP addresses or DNS name to route traffic to your accelerator, or route traffic using your own custom domain name. 

### Network Zone
  * An isolated unit with its own set of physical infrastructure... but think of it as an AWS Availability Zone. 
  * When an accelerator is configured, two IPv4 addresses will be allocated so there's always a healthy static IP address in an isolated network zone to try by a client application.

### Listener (one or more)
  * Processes inbound connections from clients to GA, based on the port and protocol (TCP or UDP protocols) you configure. 
  * Each listener has an endpoint group (or more) associated with it; traffic is forwarded to endpoints in one of the groups. 

### Endpoint Group

Each group is associated with a specific AWS Region.

  * Endpoint groups are associated with listeners by specifiying the Regions you want to distribute traffic to. 
  * Each endpoint group is associaed with a specific AWS Region
  * A "traffic dial" can increase or decrease the percentage of traffic that would be otherwise be directed to an endpoint group. 

### Endpoints
 
These can include: 

* Network Load Balancers
* Application Load Balancers
* EC2 instances
* Elastic IP addresses.

An Application Load Balancer endpoint can be an internet-facing or internal.

For each endpoint, you can configure weights (weights are numbers that you use to indicate the proportion of traffic to route to each one) so you can do things like performance testing within a region. 

## Use Cases

### Associate IP addresses with regional resources

Associate the **static IP addresses** provided by AWS Global Accelerator to regional AWS resources or endpoints, such as Network Load Balancers, Application Load Balancers, EC2 Instances, and Elastic IP addresses. 

The IP addresses are Anycast from AWS edge locations so they provide onboarding to the AWS global network close to your users.

* Anycast is a network addressing and routing methodology in which a single destination address has multiple routing paths to two or more endpoint destinations. 

#### Scenario

An online trading platform with thousands of clients across the globe is hosted in AWS. To reduce latency, you have to direct user traffic to the nearest application endpoint to the client. **The traffic should be routed to the closest edge location via an Anycast static IP address.** AWS Shield should also be integrated into the solution for DDoS protection.

Which of the following is the MOST suitable service that the Solutions Architect should use to satisfy the above requirements?

--> AWS Global Accelerator

### Move endpoints

Easily move endpoints between Availability Zones or AWS Regions without needing to update your DNS configuration or change client-facing applications.

### Dial traffic

Dial traffic up or down for a specific AWS Region by configuring a traffic dial percentage for your endpoint groups. This is especially useful for testing performance and releasing updates.

You can control traffic using traffic dials, so it's very highly configurable (this is done within the endpoint group, like an EC2 instance).

### Control traffic direction

Control the proportion of traffic directed to each endpoint within an endpoint group by assigning weights across the endpoints.

## How to Build One

See A Cloud Guru's AWS Certified Solutions Architect, Chapter 7.11 for a demo

## Compare with CloudFront

AWS Global Accelerator and Amazon CloudFront are separate services that use the AWS global network and its edge locations around the world. Both services integrate with AWS Shield for DDoS protection.

CloudFront improves performance for both cacheable content (such as **images and videos**) and dynamic content (such as **API acceleration and dynamic site** delivery). 

CloudFront doesn't have the capability to route the traffic to the closest edge location via an Anycast static IP address.

Global Accelerator improves performance for a wide range of applications over **TCP or UDP** by proxying packets at the edge to applications running in one or more AWS Regions. 

Global Accelerator is a good fit for non-HTTP use cases, such as **gaming (UDP), IoT (MQTT), or Voice over IP**, as well as for HTTP use cases that specifically require **static IP addresses** or **deterministic, fast regional** failover. 

Both services integrate with **AWS Shield** for DDoS protection.

