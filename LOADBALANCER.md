# Provision a New Load Balancer
If you still need to [create a new EC2 instance](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md), swing over to my EC2 page for instructions. 

## Go to the Loadbalancer page on AWS
Console > EC2 > Load Balancers

Click button that says `Create Load Balancer`.

### Select load balancer type
**3 Types of Load Balancers**
* Application Load Balancer
  * Evaluates the code, so it can make intelligent routing decisions - "Layer 7" 
  * Could have a group servers that are just for the payment gateway. Traffic would be sent to this group when there is a payment being made, otherwise traffic could be routed to another group of EC2 instances if a person was just browsing. 
  * Good for applications (duh)
* Network Load Balancer
  * For ultra high performance and for cases of using static IP addresses.
* Classic Load Balancer
  * Slowly getting phased out.
  * Cheaper.

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
Type in your usual URL into a browswer window and make sure all is well. 

# Resources
* A Cloud Guru's [AWS Certified Cloud Practitioner](https://acloud.guru/learn/aws-certified-cloud-practitioner) Course
* 