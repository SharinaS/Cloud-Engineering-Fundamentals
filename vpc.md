# VPC

## Index

* [Bastion Host](#Bastion-Host)
* [Create a VPC on the AWS Console](#Create-a-VPC-on-the-AWS-Console)
* [DNS Hostnames](#DNS-Hostnames)
* [NAT Instances and Nat Gateways](#NAT-Instances-and-Nat-Gateways)
  * [NAT Gateway](#NAT-Gateways)
  * [Elastic IP Address](#Elastic-IP-Address)
* [NACLs vs Security Groups](#NACLs-vs-Security-Groups)
* [Network Access Control Lists](#Network-Access-Control-Lists) (NACLs)
  * [Order of Rules](#Order-of-Rules)
* [Peering](#VPC-Peering)
* [Route Tables](#Route-Tables)
* [Security Groups](#Security-Groups)
* [What is a VPC](#What-is-a-VPC)
* [What is Created with a VPC](#What-is-Created-with-a-VPC)
* [VPC Endpoint](#VPC-Endpoint)
* [VPC Flow Logs](#VPC-Flow-Logs)

# What is a VPC

VPC = "Virtual Private Cloud"

A virtual data center in the cloud.

> A VPC is a *collection* of internet gateways, route tables, network access control lists (ACLs), security groups, EC2 instances, private and public subnets.

Amazon Virtual Private Cloud (Amazon VPC) lets you provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a **virtual network** that you define. You have complete control over your virtual networking environment.

Amazon Virtual Private Cloud (Amazon VPC) is the service that allows a customer to create a virtual network for their resources in an isolated section of the AWS cloud.

## A VPC consists of:

* IGWs (Virtual Private Gateways)
* Route Tables
* Network Access Control Lists (stateless)
* Subnets (1 subnet = 1 availability zone)
* Security Groups (stateful)

Amazon VPC lets you provision a virtual networking environment. You can fully control it, and customize it. You can have security groups and network access control lists to help control acces to AWS EC2 instances in each subnet. 

## With a VPC, we can

* Launch instances into a subnet of my choosing
* Assign custom IP address ranges in each subnet
* Configure route tables between subnets
* Create internet gateway and attach it to our VPC
* Have much better security control over your AWS resources
* Assign security groups to individual instances
* Have Subnet network access control lists (ACLS)

## Default VPC

* User friendly
* Can immediately deploy instances 
* All subnets have a route out to the internet - they're all internet accessible.

In a default VPC, all Amazon EC2 instances are assigned two IP addresses at launch - a public IP address and a private IP address.

## Use Cases

Example: A public-facing subnet for your webservers, with backend systems like a DB placed in a private-facing subnet. 

Example: A Hardware Virtual Private Network (VPN) connection between a corporate datacenter and your VPC. The AWS cloud becomes an extension of your corporate datacenter. 

## DNS Hostnames

When resolved from within the VPC, the DNS hostname will translate to the instance's private IP address. From outside the VPC, the DNS hostname will translate to the instance's public IP address.

### Default VPC

When you launch an EC2 instance into a default VPC, AWS provides it with public and private DNS hostnames that correspond to the public IPv4 and private IPv4 addresses for the instance.

The VPC is the one that enables assigning of instance hostnames.

### Custom VPC

When you launch an instance into a non-default VPC, AWS provides the instance with a private DNS hostname only. 

New instances will only be provided with public DNS hostname depending on these two DNS attributes: the DNS resolution and DNS hostnames, that you have specified for your VPC, and if your instance has a public IPv4 address.

### Scenario

You launched an EC2 instance in your newly created VPC. You have noticed that the generated instance does not have an associated DNS hostname.

In this case, the new EC2 instance does not automatically get a DNS hostname because the DNS resolution and DNS hostnames attributes are disabled in the newly created VPC.

## Subnets

1 subnet = 1 availability zone.

You can't have 1 subnet spread across multiple availability zones.

10.0.0.0/16

* This is the largest subnet you can use within the VPC, and the most commonly used. 

10.0.0.0/28
* This is the smallest subnet you can use within a VPC. 

# VPC Peering

Allows you to connect one VPC with another via a direct network route using **private IP addresses**.

Instances act as though they are on the same private network.

Can peer VPCs with other AWS accounts, and with other VPCs in the same account.

* Peering is in a star configuration (1 central VPC peers with 4 others).
* Can peer across regions

Alternative to peering: Use a Transit Gateway.

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them privately. Instances in either VPC can communicate with each other as if they are within the same network. You can create a VPC peering connection between your own VPCs, with a VPC in another AWS account, or with a VPC in a different AWS Region.

AWS uses the existing infrastructure of a VPC to create a VPC peering connection; it is neither a gateway nor a VPN connection and does not rely on a separate piece of physical hardware. 

There is no single point of failure for communication or a bandwidth bottleneck.

## Invalid Configurations

### No Transitive Peering

For instances to talk to each other, you have to directly connect a VPC to another VPC. You can't talk to an instance in secondarily connected VPCs. 

You cannot route packets directly from VPC B to VPC C *through* VPC A.

### No Edge to Edge routing

Edge to edge routing via a gateway or private connection is an invalid VPC peering configuration.

Thus, if either VPC in a peering relationship has one of the following connections, you cannot extend the peering relationship to that connection:

* A VPN connection or an AWS Direct Connect connection to a corporate network
* An internet connection through an internet gateway
* An internet connection in a private subnet through a NAT device
* A gateway VPC endpoint to an AWS service; for example, an endpoint to Amazon S3.
* (IPv6) A ClassicLink connection. You can enable IPv4 communication between a linked EC2-Classic instance and instances in a VPC on the other side of a VPC peering connection. However, IPv6 is not supported in EC2-Classic, so you cannot extend this connection for IPv6 communication.

As in, if VPC A and VPC B are peered, and VPC A has any of these connections, then instances in VPC B cannot use the connection to access resources on the other side of the connection. Similarly, resources on the other side of a connection cannot use the connection to access VPC B.

### No Overlap of CIDR Blocks

If the VPCs have multiple IPv4 CIDR blocks, you cannot create a VPC peering connection if any of the CIDR blocks overlap (regardless of whether you intend to use the VPC peering connection for communication between the non-overlapping CIDR blocks only).

## Increase Fault Tolerance

A media company has two VPCs: VPC-1 and VPC-2 with peering connection between each other. VPC-1 only contains private subnets while VPC-2 only contains public subnets. The company uses a single AWS Direct Connect connection and a virtual interface to connect their on-premises network with VPC-1.

Which of the following options increase the fault tolerance of the connection to VPC-1?

--> Establish a hardware VPN over the Internet between VPC-1 and the on-premises network.

--> Establish another AWS Direct Connect connection and private virtural interface in the same AWS region as VPC-1.

## Scenario with 3 VPCs

A large insurance company has an AWS account that contains three VPCs (DEV, UAT and PROD) in the same region. UAT is peered to both PROD and DEV using a VPC peering connection. All VPCs have non-overlapping CIDR blocks. The company wants to push minor code releases from Dev to Prod to speed up time to market.

--> Create a new VPC peering connection between PROD and DEV with the appropriate roles.

Note that you have to create a new connection, because even though DEV and PROD are both connected via UAT, these two VPCs do not have a direct connection to each other. This is because transitive peering is not allowed.

# Security Groups

These are *stateful*.

Security groups...

* evaluate all rules before deciding whether to allow traffic. 
* operate at the instance level
* support "allow" rules only.

### Security groups vs NACLs

Security Groups usually control the list of ports that are allowed to be used by your EC2 instances and the NACLs control which network or list of IP addresses can connect to your whole VPC.

## Availability Zones... when it comes to this stuff:

An availability zone in my AWS account can be a totally different availability zone from the one found in another AWS account, even though they may be called the same (ie, US-East-1A). This is because they are randomized when you choose them in the following steps.

## What is Created with a VPC

***When we create a custom VPC, it creates 3 things:*** 
 
* A default Route Table
* A Network Access Control List (Network ACL)
* A default Security Group is created (can identify it with the VPC ID)

***2 Things are NOT created***

* an Internet Gateway
* Subnet(s)

# NAT Instances and Nat Gateways

## Nat Gateway

NAT = network address translation

> **Problem**: EC2 instances for the application tier running in private subnets need to download software patches from the internet. However, the instances cannot be directly accessible from the internet. 

> **Solution**: 
> * Configure a NAT gateway in a public subnet. 
> * Define a custom route table with a route to the NAT gateway for internet traffic and associate it with the
private subnets for the application tier. 

When you're in a private subnet, how do you install updates? Install updates? Download software? Download software patches? 

Must use NAT Instances or Nat Gateways, since there is no route set up to the Internet from the private subnet. These things make it possible to **communicate with our Internet Gateway, without making our private subnet public.**

In other words, when our instances in our private subnets need to access the internet, they do so via either a NAT Instance or a NAT gateway. 

Nat Gateways are used 99% of the time. Gateways are *highly available, spread across multiple AZ's*. 

Jump down to more about [Nat Gateways](#NAT-Gateways)...

## Nat Instances

Nat Instances are *individual EC2 instances* that do this. Instances are on their way out... but they're still on the exam.

Problems with NAT Instance architecture:
* It is a single virtual machine
  * Can create high availability using Autoscaling groups, multiple subnets in different AZs, and a script to automate failover... but ouch, lots of work. 
* It's a small virtual machine, so it can easily get overwhelmed - it's a massive bottleneck.
  * Solution: increase the instance size to deal with bottle-necking.
* It's got a single point of failure. 

## How to Create a NAT *Instance*

[See Amazon docs about NAT Instances](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html). Essentially, the instance will act like a bridge to our private subnet, through our public subnet, to our internet gateway. 

Things to Note:
* When you create a NAT instance, you have to disable source/destination check on the instance.
* NAT instances must be a in a public subnet.
* There must be a route out of the private subnet *to* the NAT instance 
* A NAT instance is always *behind* a security gruop.

### Set up the NAT instance
In EC2, launch an instance.

On the left of the list of AMIs, under "Quick Start," choose "Communicty AMIs." In the search bar, type in "nat" and hit return/enter. This gives us our nat instances. 

This is the one I chose, which is an Amazon Community AMI:
```
amzn-ami-vpc-nat-hvm-2018.03.0.20181116-x86_64-ebs - ami-0b840e8a1ce4cdf15
Amazon Linux AMI 2018.03.0.20181116 x86_64 VPC HVM ebs
```

It's set as a t2.micro, which is great. 

In the configure instance details page:
* Change the vpc to your custom vpc.
* Change the subnet to the public subnet (want to launch into the public one), which will give us a public ip address, regarding the "Auto-assign Public IP" - it will say "Enable"
* Leave everything else the way it is. 

Click the button that says "Add Storage," where you do nothing, just click the button to "Add tags."

Add a few descriptive tags, definitely at least a name. 

Click "Configure security group."

Choose the "Select an existing security group" radio button so we can put this instance inside the security group that also holds the *public* instance we created earlier.

Click "Review and launch."

A window might pop up that asks where to boot from, re SSD. Choose the radio button that says, "Make General Purpose (SSD) the boot volume for this instance." If you don't get this window, don't panic - it's probably b/c of the AMI you're using, and just move on. 

Review the instance, and click "Launch"

A window will pop up asking about key pairs - choose the key pair you've used above. ... and click "Launch instances"

### Make it a "Gateway" of sorts by Disabling Source/Destination checks

Click on the NAT instance radio button. Go to the Actions tab, and choose "Networking > Enable Source/Destination Check." Go ahead and disable it. 

### Make the Database Server "talk" to the NAT Instance
To do this, we need to create a route out to the internet. The route needs to be created in the default route table, which is the main route table. 

Go to VPC (within network and content delivery), and go to "Route Tables," on the left, within the Virtual Private Cloud section. 

Click the radio button of the route table that is associated with your VPC, and says "Yes," in the "Main" column. This is the main route table associated with your VPC. 

In the tabs below, click "Routes." 

Click "Edit routes," and then "Add route." 

If we want to go out to the internet, we can add something like, "0.0.0.0/0," as our Destination. We then add our NAT instance as the Target, by clicking "Instance" on the pulldown menu and then choosing the NAT instance we created above.
* You should only allow 0.0.0.0/0 on port 80 or 443 to to connect to your public facing Web Servers, or preferably only to an ELB, says A Cloud Guru.

Click "Save routes."

Now we have a route within our main route table, which says, go over this elastic network interface (eni-).

### Terminate the Instance, since, seriously, so many issues with this architecture!

Click the radio button for the nat instance. Click the Actions pulldown menu above. Choose "Instance State > Terminate"

Remember to remove the route associated with this instance, too. Go to VPC > Route Tables.

Click on your main route table, click the tab below that says "Routes." You'll see one that says, blackhole! This is because the instance no longer exists. 

Go to "Edit routes," and click on the "x" to the right of the route, then click "Save routes."

## NAT Gateways

NAT =  network address translation

> Purpose in Life: enable instances in a private subnet to connect to the internet or other AWS services, but prevent the internet from initiating a connection with those instances.

(Other option to connect without public IP addresses: For VPCs with a hardware VPN connection or Direct Connect connection, instances can route their Internet traffic down the virtual private gateway to your existing datacenter. From there, it can access the Internet via your existing egress points and network security/monitoring devices.)

* No need to patch the OS 
* Not associated with any security groups! (Doesn't need to be behind one)
* Automatically assigned a public ip address
* No need to disable source/destination checks

### Scaleable and highly available

* It scales automatically

### Location

To create a NAT gateway, you must specify the public subnet in which the NAT gateway should reside.

### Availability Zones

You can only have one NAT gateway in an AZ.
  
To create AZ-independant architecture, you must create a NAT gateway in *each* availability zone, and configure your routing to ensure that resources use the NAT gateway in the same AZ that they're in (otherwise when a gateway goes down, things stop working)

Each NAT gateway is created in a specific Availability Zone and implemented with redundancy in that zone. You have a quota on the number of NAT gateways you can create in an Availability Zone.


### Update the Route Table

After you've created a NAT gateway, you must update the route table associated with one or more of your private subnets to point internet-bound traffic to the NAT gateway. This enables instances in your private subnets to communicate with the internet.

### Elastic IP Address

You must also specify an Elastic IP address to associate with the NAT gateway when you create it. The Elastic IP address cannot be changed after you associate it with the NAT Gateway.

An Elastic IP address is a **property of network interfaces**. An Elastic IP address is a reserved public IP address that you can assign to any EC2 instance in a particular region, until you choose to release it. 

When you associate an Elastic IP address with an EC2 instance, it replaces the default public IP address.

Associate an Elastic IP address with any instance or network interface for any VPC in your account. With an Elastic IP address, you can mask the failure of an instance by rapidly remapping the address to another instance in your VPC. 

Note that the advantage of associating the Elastic IP address with the network interface instead of directly with the instance is that **you can move all the attributes of the network interface from one instance to another in a single step**.

Elastic IP Address Billing: 

* To ensure efficient use of Elastic IP addresses, we impose a small hourly charge when they aren't associated with a running instance, or when they are associated with a stopped instance or an unattached network interface. While your instance is running, you aren't charged for one Elastic IP address associated with the instance, but you are charged for any additional Elastic IP addresses associated with the instance.

### Nat Gateway Example
Your instance is in the private subnet, it has a route in the Route Table to the NAT Gateway in the public instance. When your instance runs a yum update, it goes to the Nat Gateway, then traverses out.

### Nat Gateway Billing

NAT gateway hourly usage and data processing rates apply. Amazon EC2 charges for data transfer also apply

## Make a Nat Gateway

Go to VPC > Vitual Private Cloud > NAT Gateways.

Click the button, "Create NAT Gateway."

You'll want to choose the *public* subnet you created way back when. 

Click the button that says "Allocate Elastic IP Address," which will create an IP address and attach it to the new Gateway. 

Create the Gateway.

### Edit route tables

A window will pop up saying you'll need to edit your route tables. Click on the "Edit route tables" button.

You'll find yourself at the list of Route Tables.

Click the radio button for your *main* route table inside your VPC. Click the tab called "Routes" and then the button "Edit routes."

We need to give the main route table a route out into the internet. Click "Add route," put the destination perhaps as "0.0.0.0/0," change the "Target" to "NAT_gateway," and then choose the one you created. 

Click the "save" and "close" button.


# Network Access Control Lists 

Network Access Control List = Network ACL = NACL

> Use a network ACL to block specific IP Addresses.

"A network ACL is an optional layer of security that acts as a firewall for controlling traffic in and out of a subnet," says AWS.

VPC > Security (to the left) > Network ACLs

## Default NACL

When we create a custom VPC, a network ACL is created by default. **By default, it allows all outbound and inbound traffic**. 

Every time we add a subnet to our VPC, it will be associated with our default Network ACL. In fact, it *must* be associated with a network ACL - if you don't explicitly associate a subnet with a network ACL, the subnet will be automatically associated with the default network ACL. 

We can associate a subnet with a new NACL, but *a subnet itself can only be associated with one network ACL at a given time*. Network ACLs can have multiple subnets associated with them, however. 

* When you associate a network ACL with a subnet, the previous association is removed.

The Default NACL has Inbound Rules. Each rule is incremented by 100. 

## Custom NACLs

You can create custom network ACLs. By default each custom network ACL *denies* all inbound and outbound traffic until you add rules. 

## Order of Rules

Network ACL Rules are evaluated by rule number, from lowest to highest, and executed immediately when a matching allow/deny rule is found.

The rules are evaluated in order - 100 (HTTP), 200 (HTTPS), 300 (SSH) for inbound rules. 

Rules are evaluated starting with the lowest numbered rule. As soon as a rule matches traffic, it's applied immediately regardless of any higher-numbered rule that may contradict it.

We have 3 rules here:

1. Rule 100 permits all traffic from any source.

2. Rule 101 denies all traffic coming from 110.238.109.37

3. The Default Rule (*) denies all traffic from any source.

The Rule 100 will first be evaluated. If there is a match, then it will allow the request. Otherwise, it will then go to Rule 101 to repeat the same process until it goes to the default rule. 

If there is a request from, say, 110.238.109.37, it will go through Rule 100 first. Rule 100 says it will permit all traffic from any source, so it will allow this request and will not further evaluate Rule 101 (which denies 110.238.109.37) nor the default rule.

## Stateless

*Network ACLs are stateless*. So responses to allowed inbound traffic are subject to the rules for outbound traffic (and vice versa). 

You can add deny and allow rules. When you open a port on inbound, it doesn't mean a port opens on outbound. 

## Create a Network ACL 
Click the Create button up top. 

Create a descriptive name.

Select the VPC it should go inside. 

*Once a new Network ACL is created, note that all inbound and outbound rules are automatically set to deny everything*.

### Change Subnet Associations
Click on the new NACL's radio tab, and click on the tab below, called "Subnet associations."

Click "Edit subnet associations"

Choose the subnet you want - I chose my public one. 

The subnet is now associated with this new NACL, and the other subnet is left behind in the other one. 

Now that the Network ACL has the public subnet it in, any access via HTTP is no longer available (ie, Apache setup would have shown ability to see a little website via IP address in browser.

### Set up Rules for the New Network ACL
Click tab, "Inbound rules," then "Edit inbound rules"
* Can create rules to connect on port 80, 443, and 22, for example, inbound.

Edit Outbound rules
* connect on port 80, 443, 1024-65535 (ephemoral ports for NAT Gateway)
  * short lived transport protrocol port is an ephemeral port. They may be used on the server side of communication. Port only available during the communication, then times out. NAT gateway uses the emphemoral port of the type noted above. 

# NACLs vs Security Groups

Security Groups usually control the list of ports that are allowed to be used by your EC2 instances and the NACLs control which network or list of IP addresses can connect to your whole VPC.

*if you're using network ACLs, they will always be evaluated before security groups.* So if you **deny** a specific port on your ACL, it will never reach your security group. So, **NACls will always act first before security groups.** 

... So, you can use a network ACL to block specific IP Addresses.
* You can't block specific IP addresses using security groups.

[Sweet comparison chart on AWS](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html#VPC_Security_Comparison).

# VPC Flow Logs

A feature that lets you capture information about IP traffic going to/from network interfaces in a VPC. 

Flow log data is stored, reviewed and retrieved using **CloudWatch Logs**. 

* Alternatively you can send logs to an S3 Bucket. 

Can do logs at the VPC level, Subnet level or Network Interface level. 

You essentially go to VPC, go to the tab actions, and click "Flow Log." You also need to go over to CloudWatch and set up a new log group ("Create log group" button). This is necessary before choosing Destination log group when creating a new flow log. You'll also have to "set up permissions" (a link available when setting up a new flow log), which makes you set up a new IAM role (easy though, since it sets everything up for you). 

When you go to CloudWatch, click on "Logs" on the left hand side, and you'll see lots of data.

Note:
* You cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account. As in, you can't do this across accounts.
* You can tag flow logs
* Once you create a flow log, you cannot change its configuration. As in, you can't associate a different IAM role with the flow log. 
* Not all IP traffic is monitored. 
  * traffic generated by instances that contact the Amazon DNS server is not monitored, however if you use your own DNS server, all traffic to that DNS server is logged.
  * traffic to/from 169.254.169.254 for instance metadata not monitored
  * DHCP traffic not monitored
  * traffic to the reserved IP address for the default VPC router also not monitored.

# Bastion Host

Used to securely administer EC2 instances.

**A Bastion Host allows you to SSH or RDP into an EC2 instance located in a private subnet.** 

A special purpose computer set up to resist attacks. It's usually on the outside of a firewall or in a public subnet. Usually involves access from untrusted networks or computers. It is used to securely administer EC2 instances (using SSH or RDP).
* So, if we want to SSH into our instances in our private subnet, we do that via a bastion host. 

To create a bastion host, you can create a new EC2 instance which should only have a security group from a particular IP address for maximum security. Since the cost is also considered in the question, you should choose a small instance for your host. By default, t2.micro instance is used by AWS but you can change these settings during deployment.

> A bastion host is a special purpose computer on a network specifically designed and configured to withstand attacks. If you have a bastion host in AWS, it is basically just an EC2 instance. It should be in a public subnet with either a public or Elastic IP address with sufficient RDP or SSH access defined in the security group. Users log on to the bastion host via SSH or RDP and then use that session to manage other hosts in the private subnets.


## NAT Gateway / NAT Instance

A NAT Gateway or NAT Instance is used to provide internet traffic to EC2 instances in a private subnet. 

* Note that you can't use a NAT Gateway as a Bastion host; you have to go ahead and configure a bastion host. 

## Public Subnet

If we have a public subnet, the bastion host can go inside the public subnet. This means you can SSH or RDP through the **Internet Gateway**, through our **Route Tables**, through the **Network ACLs**, through **Security Groups**, onto the **Bastion server**. The Bastion server would then forward the connection through SSH or through RDP to our private instances. So, we just "harden" the Bastion host (since this is what will be hacked). That way we don't need to harden our private instances. 

## Scenario

You have added a bastion host with Microsoft Remote Desktop Protocol (RDP) access to the application instance security groups (instances are in both public and private subnets), but the company wants to further limit administrative access to all of the instances in the VPC.

The correct answer is to deploy a Windows Bastion host with an Elastic IP address in the public subnet and allow RDP access to bastion only from the corporate IP addresses.

# VPC Endpoint

Allows traffic between your VPC and the other service to *not leave* the Amazon network. It's a *private connection* between your VPC to supported AWS services and VPC endpoint services (powered by PrivateLink). No internet gateway, NAT device, VPN connection or AWS Direct Connect connection required! A public IP address is not required either! 

Endpoints are *virtual devices*. 

Benefits:

* Redundant
* Highly available
* No availability risks
* No bandwidth constraints

Two Types of VPC Endpoints:

* Interface 
  * Endpoints support many services
* Gateway
  * Endpoints support only S3 and DynamoDB

### How to Build One

See A Cloud Guru's AWS Certified Solutions Architect, Chapter 7.12 for a demo, which starts about 1/2 way through the chapter.

# Route Tables

A route table specifies how packets are forwarded between the subnets within your VPC, the internet, and your VPN connection.

Your VPC has an implicit router, and you use route tables to control where network traffic is directed. Each subnet in your VPC must be associated with a route table, which controls the routing for the subnet (subnet route table). You can explicitly associate a subnet with a particular route table. Otherwise, the subnet is implicitly associated with the main route table.

A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same subnet route table. You can optionally associate a route table with an internet gateway or a virtual private gateway (gateway route table). This enables you to specify routing rules for inbound traffic that enters your VPC through the gateway

Be sure that the subnet route table also has a route entry to the internet gateway. If this entry doesn't exist, the instance is in a private subnet and is inaccessible from the internet.

## Problem Solving

In cases where your EC2 instance cannot be accessed from the Internet (or vice versa), you usually have to check two things:

- Does it have an EIP or public IP address?

- Is the route table properly configured?

# Create a VPC on the AWS Console 

A VPC with Public & Private Subnets

This custom VPC is built step by step, using the AWS Console. 

"Create VPC"

Name it.

IPv4 CIDR address block:
* For the biggest block possible: `10.0.0.0/16`

IPv6 CIDR block: 
* Can use the `Amazon provided IPv6 CIDR block` - radio button.

Tenancy: `default`
* Less expensive - you're sharing your underlying hardware with other AWS customers.
* Dedicated will cost a fortune.

Hit "Create"

*To use the VPC we need to create some subnets.*

## Create Subnets
From A Cloud Guru: "In a custom VPC with new subnets in each AZ, there is a Route that supports communication across all subnets/AZs. Plus a Default SG with an allow rule 'All traffic, All protocols, All ports, from anything using this Default SG'."

Give it a name tag: Add in the CIDR block you typed in, the availability zone its in to make it easy later.

Choose the VPC you created from the dropdown list.

Choose the Availability Zone within your region. 

For IPv4 CIDR block, you can type: `10.0.1.0/24` (from CloudGuru)

For IPv6 CIDR block, I'm not assigning it; am just going to use IPv4. 

Hit "Create" 

Create another Subnet, in a different availability zone. 
* The IPv4 CIDR block I'm using for this one is: `10.0.2.0/24` 
* gonna name this one: `sharina-10.0.2.0/24-us-west-2c`

### Note that...
Note that *one subnet gets one availability zone*; no spanning across availability zones. 

Scroll to the right in list of Subnets. *The Auto-assign public IP Address is off*. We'll need to turn this on, since I want one of subnets we just created to be private and the other public. 

Also note that we only have 251 available IP addresses. We should be getting 256 (per the CIDR.xyz website), with a usable amount of 254.
* [See VPC and Subnet Sizing](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#VPC_Sizing)
  * There will always be 5 addresses that are reserved for other uses, which explains our 251 vs 256. 

## Make a Subnet Publicly Accessible

-------------

From A Cloud Guru: 
"By default, any user-created VPC subnet WILL NOT automatically assign Public IPv4 Addresses to instances – the only subnet that does this is the “Default” VPC subnets automatically created by AWS in your account." 

For a subnet to be publicly accessible, we'll need *EC2 instances* to launch in it with public IP addresses. 

------------------------

Select the subnet you want to make public - the radio button. 

Go to the tab that says "Actions," which produces a dropdown menu when you click on it. Click the "Modify auto-assign IP settings" option.

On the page that opens (which says: "Enable the auto-assign IP address setting to automatically request a public IPv4 or IPv6 address for an instance launched in this subnet. You can override the auto-assign IP settings for an instance at launch time.")... 

... click the box that says "Enable auto-assign public IPv4 address."*

Hit "Save"

The chosen subnet, within the list of subnets will show a "Yes" under the column of: "Auto-assign public IPv4 address." 

*This now lets us launch EC2 instances into this particular subnet, and that automatically allows for a public IP addresss :)*

Now, we have a public and private subnet.

Next, we need an **Internet Gateway**, which gives us a way to get into the VPC. Then, we'll need to configure our route tables. 

## Add an Internet Gateway

From A Cloud Guru: 

----------------

"The purpose of an "Egress-Only Internet Gateway" is to allow IPv6 based traffic within a VPC to access the Internet, whilst denying any Internet based resources the possibility of initiating a connection back into the VPC."

-----------

Give it a name. 

Once created, it will be "detached." You'll need to attach it to a VPC. In the list of Gateways, click the Actions, and in the pulldown choose "Attach to VPC."

Choose the VPC to attach the gateway to. 

Here's the CLI command it provided... 
```
aws ec2 attach-internet-gateway --vpc-id "vpc-092524f82494c9b63" --internet-gateway-id "igw-081c576e06ac8108f" --region us-west-2
```

*Note that you can only attach one gateway to one VPC.*

Now, we need to configure our Routes Table so we have a route out into the internets.

## Configure Routes Table

-------------

"A route table specifies how packets are forwarded between the subnets within your VPC, the internet, and your VPN connection." - AWS 

----------------

Make a route out into the Internet.

Go to Routes Table. 

Click on the radio button that corresponds to the route that belongs to the VPC you created. Check the column "VPC ID" if you're not sure which route is which).

Down below, click the tab "Routes." You'll see two routes. *These two routes are letting the two subnets I created "talk to each other."*

Click the tab "Subnet Associations." You'll see a note that says "You do not have any subnet associations." So, if we put a route out into the internet via our main route table, any subnet we create will be public by default. We always want our main route *private.* We want a *separate route table for our public subnets.*

Up above, click the button, "Create Route Table." A new page will open up. Name it and choose the VPC you want to associate it with. 
* I'm calling this one "Sharina-PublicRoute."
  * Any subnet that will be associated with this route table will be able to talk to the internet.

Back in the list of routes, I now have two routes - the original one that got created with the VPC creation, and this new one. Note that the automatically made one is the main route. 

### Make routes for IPv4 and IPv6 to the Internet Gateway:
With the radio button clicked for the Public Route you created, next, (to create a route out to the internet,) Click the tab, "Routes", and then click the button, "Edit routes." 

In the new window, click "Add route." A new row is created. Type in `0.0.0.0/0`. For Target, choose "Internet Gateway," and choose the gateway you created. 

We can now add a route out, by adding another row to the "Edit routes" table.  We can add the IPv6 by typing in `::/0` to give us a route out. In Target, choose "Internet Gateway" again, and choose the gateway you created prior.

Now, any subnet associated with the Route Table will automatically become public (based on the above settings) for IPv4 and IPv6. 

### Associate the Subnets
Given that my radio button is still clicked for my public route," I'm going to click the tab "Subnet Associations." Then, click the button, "Edit subnet associations."

Out of the two subnets that appear, click the radio button of the one you'd like to associate with your route table. 

The other subnet will then be associated will the main route table (the automatically created one).

The 0.0.0.0/0 will now point to the internet gateway. 

Now, we have a private subnet and a public subnet.

Next, we need to *provision EC2 instances* - one in the public subnet and one in the private subnet.

## Provision EC2 Instances
Go to EC2 service. Launch an instance, and choose the "Amazon Linux 2 AMI." Make sure the t2.micro is chosen, and hit "Next: configure Instance Details."

In the page that opens, for Network, choose the VPC you created. 

Choose the subnet you decided would be the public one. Be careful to be accurate with this step!

Make sure the "Auto-assign Public IP" drop down says "Use subnet setting (Enable). If it doesn't, you chose the wrong Subnet in the dropdown just above, if you're trying to put the instance in a public subnet.

### Click "Next: Add Storage." Nothing to do here, so Click "Next: Add Tags" 

Add a tag to help identify the instance. Add a key of "Name" and then write a descriptive name value.

### Click "Next: Configure Security Group"

--------------

AWS says, "... if you want to set up a web server and allow Internet traffic to reach your instance, add rules that allow unrestricted access to the HTTP and HTTPS ports."

Note that...

* a security group is "a set of firewall rules that control the traffic for your instance," says AWS.
* security groups do not span VPCs.
* when you create a new security group, all outbound traffic is allowed by default. 

-------------

Within the "Assign a security group" row, choose the radio button that says, "Create a new security group." Give it a good name and description. 

Click "Add Rule" and because we want to give the public instance HTTP access, choose the Type to be HTTP for the new rule. Meanwhile, keep the SSH type the way it is (just above the new rule you just created).

Click "Review Instance and Launch" button - this will create an HTTP instance that will allow SSH and HTTP to be open to the world. 

Then, click "Launch" button.

### Select or Create a New Key Pair

-------------

A key pair lets you securely connect to your instance. A key pair consists of:

* a public key (AWS stores this one)
* a private key (you store this one)

-------------

A window will pop up that tells you to choose or create a key pair. If you make a new key pair, remember to download the key pair and keep it somewhere you'll find again. 

Click "Launch Instance"

### Now, go back to instances, and launch a second instance. 
For this instance, follow the same steps as above, except when you get to Configure Instance Details, we want to choose the subnet that produces an "Auto-assign Public IP" statement that says *"Use subnet setting (Disable)"*. 

This is because for this instance, we are *not* wanting a public IP address for this EC2 instance. We are making an instance within our private subnet.

This way, we can create a database server within our subnet, which doesn't need to be public. 

For the "Add Tags" section, give it a key of Name and something descriptive. 

For the "Configure Security Group Section," it's fine to "Select an existing security group." Choose the "default VPC security group."

When the box pops up that says "Select an existing key pair or create a new key pair," choose the keypair you created prior (at least for the last instance). 

## Accessing the Private Instance 
*The private one does not have an IP address to connect to, since it's private*

Note that *the two instances have two separate security groups, and so, by default, the security groups do not allow access to each other.* So, if you try to SSH into the instance that is private, via the instance that is public, it's not going to work. 

Solution: Create another Security Group for the private instance that will allow for communication from the public subnet.
* *It's helpful to have a security group for the private instance, anyway, since that way it is going to be something cool like a database server :)*

Go to EC2. 

Scroll down on the left hand side to "Network and Security," and click on "Security Groups."

Click "Create security group" near the top of the page where security groups are listed.

Give it a good name and a description.

Choose the VPC you created.  

Click "Add rule" within the "Inbound rules section." - This is where we get to communicate certain things to EC2 instances inside this security group.
* If we want to ping EC2 instances inside this security group from our security group in our public subnet, for Type, choose, "All ICMP - IPv4." So the source will be our web security group, or the IP range.
  * For Source type, leave it as Custom
  * For Source, this is where we allow the public subnet's security group (the security group you created in the public subnet) to ping this one, so you can type in "sg" or start searching for the security group associated with the public subnet. 
  * Or, you can just type in what the IPv4 CIDR is, which is the IP address range. (Go to Subnets (within VPC service) to check the IPv4 CIDR, but it might be 10.0.1.0/24, for example. 
* We want to talk to our database servers using HTTP, such as when we install managment software to manage mySQL, so add another rule.
  * For Type, choose "HTTP." For Source, type in the IP address range, or search for the security group name. In my case, it's "10.0.1.0/24" 
* We want HTTPS, so choose this in Type, and add in the IP address range in Source.
* We also want SSH, so choose this in Type, and add in the IP address range in Source.
* For a database, we need MySQL/Aurora, which allows us to communicate to DB server using MySQL. Do the same for Source. 

Leave the outbound rules the way they are - it should already have Type "All traffic," and have destination "0.0.0.0/0", "::/0"

Click "Create Security Group"

![screenshot of inbound rules for this security group](/assets/dbserversecuritygroup.png)

**Now, we need to move the instance in the private subnet we created into this new security group we just created for it**


Go to EC2. Click on the radio button for the instance in the private subnet you created a while back.

Up above, click the pulldown menu called "Actions," and choose "Networking > Change Security Groups."

A new window will pop up, called "Change Security Groups." You'll see that the default security group is currently chosen. Unclick it, and instead click your newly created security group. Hit the button to confirm all that.

## Test The Things
### SSH into the public EC2 instance

Via the terminal, navigate to where you stored your KeyPair. 

If it's *a new key pair, you'll have to establish the appropriate permissions with a chmod command:

```
chmod 400 SharinaKeyPair.pem 
```

Grab the IPv4 Public IP value (the IP address) of the public instance you made. 

To SSH into the instance, type into the terminal the following, plus the IP address, plus the file name for the pem file. 

```
ssh ec2-user@the-ip-address -i file-name-for-your-key-pair.pem
```
Elevate priviledges to root:

```
sudo su
```

### Test the Private Instance
Back in the list of EC2 instances, with your radio button still checked, go down to the tab called "Description," and copy to clipboard the private IP address, titled "Private IPs."

Via the terminal, navigate to where you stored your KeyPair. 

```
ssh ec2-user@the-ip-address -i file-name-of-your-key-pair.pem
```

Do an update if it tell you to.
```
sudo yum update
```

Elevate priviledges to root:

```
sudo su
```

```
ping private-ip-address-of-private-instance
```


# Learning Moments
Hardest part to building a VPC, when doing this for the first time, is making sure that one is working within or with the correct subnet, given the goals for that subnet. So, labeling each subnet descriptively is really important. Actually, label *everything* descriptively. I didn't at first, and that got me in a bit of confused trouble at times. 


# Resources
[Practical VPC Design on Medium](https://medium.com/aws-activate-startup-blog/practical-vpc-design-8412e1a18dcc)

[A Cloud Guru's AWS Certified Solutions Architect Associate course](https://acloud.guru/)

[AWS NAT Instances docs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html)

[AWS' "What Is Amazon VPC" doc](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)

[Reference for Bastion Architecture/Configuration ](https://docs.aws.amazon.com/quickstart/latest/linux-bastion/architecture.html)

