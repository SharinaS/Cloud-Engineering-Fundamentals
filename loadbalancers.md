# LoadBalancer

## Index 

[3 Types of Elastic Load Balancers](#3-Types-of-Elastic-Load-Balancers)

[About Load Balancers](#About-Load-Balancers)

[Cross Zone Load Balancing](#Cross-Zone-Load-Balancing)

[Errors One May See](#Errors-One-May-See)

[Path Patterns](#Path-Patterns)

[Sticky Sessions](#Sticky-Sessions)

[X-Forwarded-For Header](#X-Forwarded-For-Header)

# About Load Balancers

Elastic Load Balancing distributes incoming application or network traffic across multiple targets, such as Amazon EC2 instances, containers, and IP addresses, in multiple Availability Zones. Elastic Load Balancing scales your load balancer as traffic to your application changes over time. It can automatically scale to the vast majority of workloads.

A physical or virtual device that helps you balance the load across web servers. Can also be used for applications. It doesn't need to be internet facing, though usually they are.

Instances monitored are reported as either:

* InService
* OutofService

Health Checks of an Instance:

* Performed by "talking" to the instance

Load balancers don't have an IP address; you are supplied a DNS name for the load balancer.

## Benefits

A load balancer distributes workloads across multiple compute resources, such as virtual servers. Using a load balancer **increases the availability and fault tolerance of your applications.**

You can **add and remove compute resources** from your load balancer as your needs change, without disrupting the overall flow of requests to your applications.

You can **configure health checks**, which monitor the health of the compute resources, so that the load balancer sends requests only to the healthy ones. 

You can also offload the work of **encryption and decryption** to your load balancer so that your compute resources can focus on their main work.

See also [ELB features on AWS](https://aws.amazon.com/elasticloadbalancing/features/#compare).

## AZs, Not Regions

Elastic Load Balancers distribute traffic among EC2 instances across multiple Availability Zones but not across AWS regions. 

* Use Route53 Geolocation Routing to send queries from a particular region to a particular region, instead, if you need cross-region traffic.

# 3 Types of Elastic Load Balancers

See also [Product comparisons on this AWS doc](https://aws.amazon.com/elasticloadbalancing/features/#compare)

## Application Load Balancer

An Application Load Balancer functions at the application layer, the seventh layer of the Open Systems Interconnection (OSI) model - application-aware.

After the load balancer receives a request, it evaluates the listener rules in priority order to determine which rule to apply, and then selects a target from the target group for the rule action. You can configure listener rules to route requests to different target groups based on the content of the application traffic. Routing is performed independently for each target group, even when a target is registered with multiple target groups.

* It can see requests you're making and inside the HTML, etc, so it can make advanced routing, so specific requests can be made to specific web servers.

### Use Cases

* Could have a group of servers that are just for the payment gateway. Traffic would be sent to this group when there is a payment being made, otherwise traffic could be routed to another group of EC2 instances if a person was just browsing. 
* Load balancer can see that language is changed to French, and load balance across all the French servers.  
* The need for a load balancer used by an online application which requires path-based routing, host-based routing, and bi-directional communication channels using WebSockets.

Best suited for 

* load balancing of HTTP and HTTPS traffic
* flexible application management
* TLS termination <-- (Transport Layer Security)
* path-based routing, host-based routing 
* support for containerized applications

### Path-based routing

The only type to support path-based and host-based routing!

## Network Load Balancer

For ultra high performance.

Best suited for 

* load balancing of TCP traffic where *extreme performance* is required.
* using static or fixed IP addresses

Can handle millions of requests per second, while maintaining ultra-low latencies

* Like a Tesla Roadster

Operates at the connection level (Layer 4).

Expensive

### Benefits

Ability to handle volatile workloads and scale to millions of requests per second.

Support for static IP addresses for the load balancer. You can also assign one Elastic IP address per subnet enabled for the load balancer.

Support for registering targets by IP address, including targets outside the VPC for the load balancer.

Support for routing requests to multiple applications on a single EC2 instance. You can register each instance or IP address with the same target group using multiple ports.

Support for containerized applications. Amazon Elastic Container Service (Amazon ECS) can select an unused port when scheduling a task and register the task with a target group using this port. This enables you to make efficient use of your clusters.

Support for monitoring the health of each service independently, as health checks are defined at the target group level and many Amazon CloudWatch metrics are reported at the target group level. Attaching a target group to an Auto Scaling group enables you to scale each service dynamically based on demand.

## Classic Load Balancer

Slowly getting phased out - legacy Elastic Load Balancers

Best suited for:

* an application built within the EC2 Classic network

Cheaper.

Can load balance HTTP/HTTPS applications, and layer 7-specific features 

* such as X-Forwarded and sticky sessions
* Not application aware, since it doesn't do stuff at the layer 7 level; not very intelligent of a load balancer.

Can use strict Layer 4 load balancing for applications that rely soley on the TCP protocol

Can use some CloudWatch metrics.

# Security Features

## Perfect Forward Secrecy

Perfect Forward Secrecy is a feature that provides additional safeguards against the eavesdropping of encrypted data, through the use of a unique random session key. This prevents the decoding of captured data, even if the secret long-term key is compromised.

CloudFront and Elastic Load Balancing are the two AWS services that support Perfect Forward Secrecy.

# Errors One May See

### Error 504
App stops responding and the Classic Load Balancer responds with a 504 error. 

This means the the *application* is having problems, maybe at the Web Server layer or the Database Layer. 

> The gateway has timed out, meaning the aplication is not responding within the idle timeout period. 

Solution: Identify where the application is failing - Web Server layer or the Database Server layer. Scale it up or out, where possible. 


# X Forwarded For Header

If you need the IPv4 address of your end user: 
Can get the client's public IP address, by looking for the X-Forwarded-For header. 

The client's IP address gets forwarded to the load balancer; the load balancer's IP address gets sent over to the EC2 instance. The EC2 instance then logs the load balancer's IP address as the client's IP address.

# Sticky Sessions

Lets you bind a user's session to a specific EC2 instance (in a **classic load balancer**) or to a Target Group Level (in a **application load balancer**).

All requests from the user during the session wil thus be sent to the same instance (or target group level). 

## Use Cases

... a file is being uploaded or downloaded, for example, and the instance needs to stay the same. 

... you're writing to an EC2 instance, like a local disk, where you are storing information locally to that instance.

### Scenario: 
User visits a website that's behind a classic load balancer. You notice that the traffic is consistently going to a single instance, versus also to the other instance. 
* Solution: Disable sticky sessions.

# Cross Zone Load Balancing

## Cross Zone Load Balancing Disabled

> A load balancer cannot normally send traffic from one AZ to another AZ.

user --> Route53, which is splitting traffic 50:50 to two AZs --> Each AZ has a classic load balancer.

One AZ has 4 instances,so each instance will get 12.5% of traffic.

The other AZ has a single instance. That instance will get 50% of traffic.

## Cross Zone Load Balancing Enabled

### Classic Load Balancer

With cross-zone load balancing, each load balancer node for your Classic Load Balancer distributes requests evenly across the** registered instances** in all enabled Availability Zones. If cross-zone load balancing is disabled, each load balancer node distributes requests evenly across the registered instances in its Availability Zone only.

#### Examples: 

(1) User sends traffic to Route53, which splits the traffic 50:50 into two AZs - each AZ has a class load balancer. One AZ has 4 instances. The other AZ has a single instance. With cross zone load balancing, the load balancer here can send traffic not only to the single instance, but also to the instances in the other AZ. The 5 instances in total, will therefore get 20% of the traffic.

(2) Problem: Traffic from users are going from Route53 to a single load balancer, which is sending traffic onto its 4 instances. You notice that your other AZ is not getting any traffic at all. Solution: Enable cross zone load balancing.

### Elastic Load Balancing

The nodes for your load balancer distribute requests from clients to registered targets. When cross-zone load balancing is enabled, each load balancer node distributes traffic across the **registered targets** in all enabled Availability Zones. When cross-zone load balancing is disabled, each load balancer node distributes traffic only across the registered targets in its Availability Zone.

# Path Patterns

Path-based routing means creating a listener that has rules to forward requests (direct traffic) based on the URL path.

Perks:
* This means that if you have microservices, you can route traffic to multiple backend services.
* You can route general requests to one target group, and requests to render images to another target group.

### Scenario
traffic from users --> route53 --> a single AZ's application load balancer

You want to have the home url sent to one AZ, and the url with, say, /images/ will be sent to a separate AZ. You have the load balancer take care of the Path Patterns that will organize the traffic destinations, re: AZs. 


# Build a Simple One in the AWS Console

Provision a New Load Balancer via the Console - Super Basic Example
Notes inspired by the Certified Cloud Practitioner course with A Cloud Guru.

## Launch an instance
If you still need to [create a new EC2 instance](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md), swing over to my EC2 page for instructions. 


## Go to the Loadbalancer service on AWS
Console > EC2 > Load Balancers

Click button that says `Create Load Balancer`.

### Select load balancer type
Choose "Application Load Balancer" by clicking `Create`.

### Step 1: Configure Load Balancer
* Give it a name
* Scheme should be internet facing 
  * if you want to access it from the internet, which we do for a web server.
* Keep the HTTP listener at port 80
* The VPC (virtual data center) can be kept at default (created when we create an AWS account)
  * Our EC2 instance is sitting in the VPC currently. 
  * Choose all Availability Zones - we want EC2 instances in every single AZ. If one goes down, we have backup, in other words. 

Click `Next: Configure Security Settings`

### Step 2: Configure Security Settings
Click `Next` if you don't mind using HTTP for now, versus HTTPS.

### Step 3: Configure Security Groups
Choose the Security Group that coincides with the project you're wanting to add a load balancer to. 

Click `Next: Configure Routing`

### Step 4: Configure Routing
* Give a name, such as "MyWebServers"
* Leave everything the way it is for a basic setup.

The Advanced health check is where it wil poll the port every 30 seconds (the interval), and if it gets 5 responses back (the healthy threshold), then the instance is healthy.
* Might be a good idea to change the healthy threshold to 3, and the timeout changed to 3, and the interval changed to 5.

Unhealthy threshold is how many times it polls; if it's unhealthy itwill timeout. 

Click `Next: Register Targets`

### Step 5: Register Targets
Click on the instance you want to register to run behind the load balancer.

Click on `Add to registered`, just above. 

Click `Next: review`

### Step 6: Review
Click `Create`

### Load Balancer Creation Status
![Screenshot of notification that load balancer was created](/assets/load-balancer.png)

### Test Load Balancer
Go to screen that lists the Load Balancers you've got. Toggle the load balancer you want to check. 

Under Basic Configuration, down below, copy to clipboard the DNS name. Paste that into a new browser window. Your website should appear. 

Take the EC2 instance and add it behind the load balancer.
* Go to Target Groups (nav bar on left hand side)
* Click on the tab `Targets` below the toggled group.

Can Add another instance by clicking on `Edit`, just above "Registered targets."

------------------------

# Second Example Demo in the AWS Console

Set up a Classic Load Balancer Via the Console 
Includes Bootstrap script with user data to create webservers using Apache. 

## Set up Instance 1
Add to the VPC of choice (default is fine for experimenting). 

Add to the subnet you want it to be in (a public subnet)

Can add in a bootstrap script to install and start Apache service, and write a little web page in our var/www directory. 

User Data Option 1:
```
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>Hello World</h1><html>" > index.html
```

User Data Option 2:
```
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>Hello from the $(curl http://169.254.169.254/latest/meta-data/instance-id) instance!</h1><html>" > index.html
```

User Data Option 3: 
```
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
echo "<html><h1>Hello from the $(curl http://169.254.169.254/latest/meta-data/instance-id) instance!</h1><html>" > /var/www/hmtl/index.html
```
you may need to mkdir /var/www/html if the Apache install doesn’t make it for you

[Resource on Apache](https://serverfault.com/questions/125865/finding-out-what-user-apache-is-running-as)

Meanwhile...

Can keep storage as default.

Add tags, specifically a Name.

Configure the Security Group - use a self-created security group (which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules) as a basic experiment.

Launch the thing.

## Set up Instance 2

Do the same as above, except make sure to put your new instance into its *own* availability zone. 

And, Change the user data to have an `<h1>` tag of Hello World Again!, or something of the sort. 

Change the tags to show the unique instance's name

## Check that the boostrap worked
Navigate to the public IP address of instance 1 and then of of instance 2 via a browser window. Tadaa! WebServer 1 and Webserver 2 are up and running. 

## Create a Classic LoadBalancer
Give it a name. 

Choose the VPC to put it in.

> If you want to have a loadbalancer inside of a *private subnet*, you can create an "internal load balancer" at this stage. No need for this particular example.

> If you want to choose what subnets your elastic load balancer is going to be deployed into, you can check the box that says "Enable advanced VPC configuration." Note that you should have a minimum of two. No need for this particular example.

You can add to your Listener Configuration, but for this example, we can leave it with the default HTTP on port 80.

Click Next.

Use a self-created security group (which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules) for this example.

Click next and next.

"Configure Health Check" is where we tell it what to check. We want it to ping our index.html

Set up the Health Check configuration to say that in 10 seconds it will detect whether or not the EC2 instance is failing, and it will take 15 seconds for it to identify it as healthy:
* Response timeout - time to wait for a response from the health check - change to 2 seconds.
* Intervial - time between health checks - change to 5 seconds
* Unhealthy threshold - number of consecutive health check failures before declaring an EC2 instance as unhealthy - change to minimum of 2. 
* Healthy threshold - change to 3. 

Click Next.

Add the two instances you created by checking their radio buttons. 

You can "Enable Cross-Zone Load Balancing, which means you distribute traffic across all targets in the AZs that are enabled for the balancer. 

"Connection Draining" means that you allow traffic to continue flowing for 300 seconds, or 5 minutes. 

Add tags and create the load balancer.

Upon creation, the load balancer is given a DNS name
* We are never given an IP address for an elastic load balancer

## Test the Load Balancer

Copy the DNS name into your clipboard once the instances pass their health check (see the Instances tab).

Paste the DNS name into a browser window. 

> If you do hard refreshes, you'll sometimes get your first website, and sometimes your second website. 

Try stopping the first instance (stop, not terminate). The instance will stop serving HTTP traffic to the load balancer. 
* The instance will then be marked as unhealthy by the load balancer, and taken out of the load balancing pool.

In the Load Balancer service, the instance that was stopped will be marked as "Out of service." Hard refreshes of DNS name in the browser window will now only produce the second website. 


## Delete the load balancer

... if you don't need it, so you don't incur excessive fees. 

# 3rd Example in the AWS Console - ALB

Set up an Application Load Balancer via the AWS Console
Set up two instances (see above in the section about setting up a classic load balancer). 

## Set up a Target Group
In the EC2 service, on the left, click on "Target Groups" and create one. 
> Target group - where your load balancer will route requests to the targets in the target group, including performing health checks. Allows you to have a group of EC2 instances for your Indian customers, a group for your European customers, etc, with different language settings, etc. You can then create application load balancers that detect a setting and then send traffic to the correct group.

Give the target group a name

Target type should be instance, since we're working with instances in this example, but it can also be IP or lambda.
* Use an IP if you have a web server that is not within AWS, where you just have the public IP address. 

For this example, it's fine to keep Protcol as HTTP on Port 80, with a default VPC. 

For Health check settings, add in a Path, which in this example, is /index.html (from the website simply created with Bootstrap above).

Change the Advanced health check settings as needed, to the settings described in creating the Classic Load Balancer above.

## Add Instances
With your target group selected, scroll down to the "Targets" tab and click "Edit."

Add the instances you want to add to the target group, and click "Save."

## Create a Load Balancer
When you select the type, choose the "Application Load Balancer" for this step-through.

Give it a name.

We want to make it internet-facing, so in "Scheme," it should say "internet-facing," and under Listeners, it should say HTTP Load Balancer Protocol, with Port 80. 

In "Availability Zones, choose the VPC (default is fine for this basic step-through), and you can choose the AZ's (defined by the subnets) you want to use. All is fine for this example. 

Click next. Add the load balancer to the security group you'd like, such as the one you've created before for a public instance, which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules. 

Next, we can "Configure Routing," where we can use the target group we created prior. 

Click next. It'll say there are no registered targets. Click next and then "Create." 

## Go to Target Groups and Register Targets
With the target group's radio button checked, click on the tab, "Targets," and click on "Edit."  

Click on the instances you want, which are the new targets. Click "Add to registered" button on port 80 (given this example). Then, click "Save." 

Now, wait for status to update from initial to healthy. Once that happens, the targets will be behind the application load balancer. 

## Test the Application Load Balancer
In the load balancer list, go down to the tab, "Description" for your load balancer, and copy the DNS name. 

Paste the DNS name into a browser window. Refresh it several times to see the two different web sites :) 

# If Load Balancer is Setup After Route 53...
From [AWS route 53 faqs](https://aws.amazon.com/route53/faqs/):
"Amazon Route 53 offers a special type of record called an 'Alias' record that lets you map your zone apex (example.com) DNS name to the DNS name for your ELB load balancer (such as my-loadbalancer-1234567890.us-west-2.elb.amazonaws.com). IP addresses associated with load balancers can change at any time due to scaling up, scaling down, or software updates. Route 53 responds to each request for an Alias record with one or more IP addresses for the load balancer."

## Go to Load Balancer on Your Console
Copy to clipboard the IPv4 address of the load balancer you want Route 53 to point to.

## Go to Route 53
Click the radio button for the domain name you're working with, and click the button just above that says `Go to Record Sets.` 

Choose the radio button for the domain name, Type A, with a value of the old ALIAS you'd like to replace.  

### Under "Edit Record Set" 
Copy the IPv4 address you copied into the box directly to the right of "Alias Target".

![screenshot of changing where route 53 points](/assets/route53-change.png)

Click `Save record set`

### Check domain name
Type in your usual URL into a browser window and make sure all is well. 

------------------

# Resources
* A Cloud Guru's [AWS Certified Cloud Practitioner](https://acloud.guru/learn/aws-certified-cloud-practitioner) Course
* A Cloud Guru's AWS Certified Solutions Architect Associate Course
* [AWS FAQs on Load Balancers](https://aws.amazon.com/elasticloadbalancing/faqs/)
* [AWS's What Is an Application Load Balancer?](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
* [Cloud Formation and Load Balancing](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_ElasticLoadBalancingV2.html)