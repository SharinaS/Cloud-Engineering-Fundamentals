# Global Accelerator

A service in which you create accelerators to improve availability and performance of your app for local and global users. Global Accelerator directs traffic to optimal endpoints over the AWS global (backbone) network. Much more efficient. 

GA gives you *two static IP addresses* that you associate with your accelerator. You can also bring your own IP addresses. 

User --> Edge location --> AWS Global Accelerator --> Endpoint group --> Endpoints

You can control traffic using traffic dials, so it's very highly configurable (this is done within the endpoint group, like an EC2 instance).

### Components to a Global Accelerator
* Static IP addresses
* Accelerator
* DNS name (supplied with set up of accelerator)
  * will point to the static IP addresses that GA assigns to you. 
  * can use your accelerator's static IP addresses or DNS name to route traffic to your accelerator, or route traffic using your own custom domain name. 
* Network Zone
  * An isolated unit with its own set of physical infrastructure... but think of it as an AWS Availability Zone. 
  * When an accelerator is configured, two IPv4 addresses will be allocated so there's always a healthy static IP address in an isolated network zone to try by a client application.
* Listener (one or more)
  * Processes inbound connections from clients to GA, based on the port and protocol (TCP or UDP protocols) you configure. 
  * Each listener has an endpoint group (or more) associated with it; traffic is forwarded to endpoints in one of the groups. 
* Endpoint Group
  * Endpoint groups are associated with listeners by specifiying the Regions you want to distribute traffic to. 
  * Each endpoint group is associaed with a specific AWS Region
  * A "traffic dial" can increase or decrease the percentage of traffic that would be otherwise be directed to an endpoint group. 
* Endpoint
  * These are Network Load Balancers, Application Load Balancers, EC2 instances, Elastic IP addresses.
  * An Application Load Balancer endpoint can be an internet-facing or internal.
  * For each endpoint, you can configure weights (weights are numbers that you use to indicate the proportion of traffic to route to each one) so you can do things like performance testing within a region. 

### How to Build One
See A Cloud Guru's AWS Certified Solutions Architect, Chapter 7.11 for a demo
