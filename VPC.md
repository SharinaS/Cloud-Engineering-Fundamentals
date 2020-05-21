# VPC

## Index - not complete

[NAT Instances and Gateways](#NAT-Instances-and-Gateways)

# What is a VPC

VPC = "Virtual Private Cloud"

A virtual data center in the cloud.

A VPC is a *collection* of internet gateways, route tables, network access control lists (ACLs), security groups, EC2 instances, private and public subnets.

> Amazon Virtual Private Cloud (Amazon VPC) lets you provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define. You have complete control over your virtual networking environment. --*AWS Certified Cloud Practitioner Practice Exam Course*

## A VPC consists of:

* IGWs (Virtual Private Gateways)
* Route Tables
* Network Access Control Lists (stateless)
* Subnets (1 subnet = 1 availability zone)
* Security Groups (stateful)

Amazon VPC lets you provision a virtual networking environment. You can fully control it, and customize it. You can have security groups and network access control lists to help control acces to AWS EC2 instances in each subnet. 

## With a VPC, we can:

* Launch instances into a subnet of my choosing
* Assign custom IP address ranges in each subnet
* Configure route tables between subnets
* Create internet gateway and attach it to our VPC
* Have much better security control over your AWS resources
* Assign security groups to individual instances
* Have Subnet network access control lists (ACLS)

## Default VPC:

* User friendly
* Can immediately deploy instances 
* All subnets have a route out to the internet - they're all internet accessible.

In a default VPC, all Amazon EC2 instances are assigned two IP addresses at launch - a public IP address and a private IP address.

## Examples
Example: A public-facing subnet for your webservers, with backend systems like a DB placed in a private-facing subnet. 

Example: A Hardware Virtual Private Network (VPN) connection between a corporate datacenter and your VPC. The AWS cloud becomes an extension of your corporate datacenter. 

## Subnets
1 subnet = 1 availability zone.

You can't have 1 subnet spread across multiple availability zones.

10.0.0.0/16
* This is the largest subnet you can use within the VPC, and the most commonly used. 

10.0.0.0/28
* This is the smallest subnet you can use within a VPC. 

## VPC Peering
Allows you to connect one VPC with another via a direct network route using private IP addresses. 

Instances act as though they are on the same private network.

Can peer VPCs with other AWS accounts, and with other VPCs in the same account.
* Peering is in a star configuration (1 central VPC peers with 4 others).
* Can peer across regions

## Transitive Peering
Instances within one VPC can talk to the instances within a connected VPC. For instances to talk to each other, you have to directly connect a VPC to another VPC. You can't talk to an instance in secondarily connected VPCs. 

In other words, there is *no transitive peering!*

## Security Groups

These are *stateful*.

Security groups...

* evaluate all rules before deciding whether to allow traffic. 
* operate at the instance level
* support "allow" rules only.

## Network Access Control Lists (ACLs)

These are *stateless*. 

You can add deny and allow rules. When you open a port on inbound, it doesn't mean a port opens on outbound. 

## Availability Zones... when it comes to this stuff:

An availability zone in my AWS account can be a totally different availability zone from the one found in another AWS account, even though they may be called the same (ie, US-East-1A). This is because they are randomized when you choose them in the following steps. 

---------------
---------------

# Create a Custom VPC - VPC with Public & Private Subnets

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

***When we create a custom VPC, it creates 3 things:***  
* A default Route Table
* A Network Access Control List (Network ACL)
* A default Security Group is created (can identify it with the VPC ID)

***2 Things are NOT created***
* an Internet Gateway
* Subnet(s)


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
From A Cloud Guru: 
*"By default, any user-created VPC subnet WILL NOT automatically assign Public IPv4 Addresses to instances – the only subnet that does this is the “Default” VPC subnets automatically created by AWS in your account." The simplest way to make the instance reachable from the outside world is therefore to **create an elastic IP address and associate it with your instance.***

For a subnet to be publicly accessible, we'll need *EC2 instances* to launch in it with public IP addresses. 

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
From A Cloud Guru: "The purpose of an "Egress-Only Internet Gateway" is to allow IPv6 based traffic within a VPC to access the Internet, whilst denying any Internet based resources the possibility of initiating a connection back into the VPC."

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
*"A route table specifies how packets are forwarded between the subnets within your VPC, the internet, and your VPN connection." - AWS* 

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
AWS says, "... if you want to set up a web server and allow Internet traffic to reach your instance, add rules that allow unrestricted access to the HTTP and HTTPS ports."

Note that...
* a security group is "a set of firewall rules that control the traffic for your instance," says AWS.
* security groups do not span VPCs.
* when you create a new security group, all outbound traffic is allowed by default. 

Within the "Assign a security group" row, choose the radio button that says, "Create a new security group." Give it a good name and description. 

Click "Add Rule" and because we want to give the public instance HTTP access, choose the Type to be HTTP for the new rule. Meanwhile, keep the SSH type the way it is (just above the new rule you just created).

Click "Review Instance and Launch" button - this will create an HTTP instance that will allow SSH and HTTP to be open to the world. 

Then, click "Launch" button.

### Select or Create a New Key Pair
A key pair lets you securely connect to your instance. A key pair consists of:
* a public key (AWS stores this one)
* a private key (you store this one)

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
-------------

# NAT Instances and Gateways
NAT = network address translation

When you're in a private subnet, how do you install updates? Install updates? Download software? Must use NAT Instances or Nat Gateways, since there is no route set up to the Internet from the private subnet. These things make it possible to communicate with our Internet Gateway, without making our private subnet public.

In other words, when our instances in our private subnets need to access the internet, they do so via either a NAT Instance or a NAT gateway. 

Nat Gateways are used 99% of the time; Nat Instances are *individual EC2 instances* that do this. Gateways are *highly available, spread across multiple AZ's*. Instances are on their way out... but they're still on the exam.

Problems with NAT Instance architecture:
* It is a single virtual machine
  * Can create high availability using Autoscaling groups, multiple subnets in different AZs, and a script to automate failover... but ouch, lots of work. 
* It's a small virtual machine, so it can easily get overwhelmed - it's a massive bottleneck.
  * Solution: increase the instance size to deal with bottle-necking.
* It's got a single point of failure. 

## How to Create a NAT *Instance*:
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

## Create a NAT Gateway - scaleable and highly available
Things to Note:
* They are redundant inside the availability zone, which means you can have more than 1 internet gateway for your instances per VPC.
* You can only have one NAT gateway in an AZ.
  * To create AZ-independant architecture, you must create a NAT gateway in *each* availability zone, and configure your routing to ensure that resources use the NAT gateway in the same AZ that they're in (otherwise when a gateway goes down, things stop working).
* Preferred by the enterprise
* It scales automatically
* No need to patch the OS 
* Not associated with any security groups! (Doesn't need to be behind one)
* Automatically assigned a public ip address
* No need to disable source/destination checks

How it works:
Your instance is in the private subnet, it has a route in the Route Table to the NAT Gateway in the public instance. When your instance runs a yum update, it goes to the Nat Gateway, then traverses out. 

### Make a Gateway
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

---------------

# Network Access Control Lists (Network ACL)
"A network ACL is an optional layer of security that acts as a firewall for controlling traffic in and out of a subnet," says AWS.

Go to VPC > Security (to the left) > Network ACLs.

You'll see a list of Access Control Lists. Two were created at some point along the journey - one with 2 subnets (sits inside our custom VPC), and one with 3 subnets (sits within the default VPC). 

In other words, when we created the custom VPC, a network ACL was created by default. By default, it allows all outbound and inbound traffic. 

Everytime we add a subnet to our VPC, it will be associated with our default Network ACL. In fact, it *must* be associated with a network ACL - if you don't explicitly associate a subnet with a netowrk ACL, the subnet will be automatically associated with the default network ACL. 

Note, we can associate a subnet with a new NACL, but *a subnet itself can only be associated with one network ACL at a given time*. Network ACLs can have multiple subnets on them, however. 
* When you associate a network ACL with a subnet, the previous association is removed.

The Default NACL has Inbound Rules. Each rule is incremented by 100. (see more below).

You can create custom network ACLs. By default each custom network ACL *denies* all inbound and outbound traffic until you add rules. 

*if you're using network ACLs, they will always be evaluated before security groups.* So if you **deny** a specific port on your ACL, it will never reach your security group. So, NACls will always act first before security groups. 

... So, you can use a network ACL to block specific IP Addresses.
* You can't block specific IP addresses using security groups.

*Network ACLs are stateless*. So responses to allowed inbound traffic are subject to the rules for outbound traffic (and vice versa). 

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

### About the Order of Rules:
The rules are evaluated in order - 100 (HTTP), 200 (HTTPS), 300 (SSH) for inbound rules. So, if there's a deny rule setup as rule 400, the deny won't activate till everything else is done. If you set up a deny rule for a specific IP address, the deny rule needs to be numbered 99, in this case, so it's before the allow rules. 

----------------
# Custom VPCs and Elastic Load Balancers
When you provision a load balancer, you'll need *at least two* public subnets. Otherwise, it won't let you create a load balancer. 

That is all for now, regarding this topic.

-----------------
# VPC Flow Logs
A feature that lets you capture information about IP traffic going to/from network interfaces in a VPC. 

Flow log data is stored, reviewed and retrieved using CloudWatch Logs. 
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

-----------------
# Bastion Host
Used to securely administer EC2 instances.

**A Bastion Host allows you to SSH or RDP into an EC2 instance located in a private subnet.** 

A special purpose computer set up to resist attacks. It's usually on the outside of a firewall or in a public subnet. Usually involves access from untrusted networks or computers. It is used to securely administer EC2 instances (using SSH or RDP).
* So, if we want to SSH into our instances in our private subnet, we do that via a bastion host. 

Note that a NAT Gateway or NAT Instance is used to provide internet traffic to EC2 instances in a private subnet. 

If we have a public subnet, the bastion host can go inside the public subnet. This means you can SSH or RDP through the Internet Gateway, through our Route Tables, through the Network ACLs, through Security Groups, onto the Bastion server. The Bastion server would then forward the connection through SSH or through RDP to our private instances. So, we just "harden" the Bastion host (since this is what will be hacked). That way we don't need to harden our private instances. 

Note that you can't use a NAT Gateway as a Bastion host; you have to go ahead and configure a bastion host. 

-----------------
# Direct Connect
A direct connection from your data center into AWS, using dedicated lines. It is an AWS service. 

Allows for private connectivity between AWS and a datacenter, office, etc. 

Direct connect locations are spread all over the world. 

Within direct connect is a "AWS cage," which has routers. Customers routers then connect to the routers within the AWS cage. None of this traverses the internet. There is use of the AWS backbone network. 

Useful for high throughput workloads (ie lots of network traffic). 

Useful for when you need a stable and reliable and secure connection. For example, if you have situation where you're trying to use a VPN connection but it keeps dropping out b/c of the amount of throughput, you can use a direct connect to solve those issues. 

## Steps for Setup
Note that if one does not have a direct connect connection at your location, one can't do these steps. 

1. Create a virtual interface in the Direct Connect console - this is a Public Virtual Interface.
2. Go to the VPC console > VPN connections. Create a Customer Gateway.
3. Create a Virtual Private Gateway
4. Attach the Virtual Private Gateway to the desired VPC.
5. Select VPN Connections and create new VPN Connection
6. Select the Virtual Private Gateway and the Customer Gateway (both of which make up a VPN connection)
7. Once the VPN is available, set up the VPN on the customer gateway or firewall.

^-- memorize these steps for the exam and watch this video on YouTube, called [How do I configure a VPN over AWS Direct Connect?](https://www.youtube.com/watch?v=dhpTTT6V1So). 

-----------------
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

-----------------
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

-----------------
# Learning Moments
Hardest part to building a VPC, when doing this for the first time, is making sure that one is working within or with the correct subnet, given the goals for that subnet. So, labeling each subnet descriptively is really important. Actually, label *everything* descriptively. I didn't at first, and that got me in a bit of confused trouble at times. 



-----------------

# Resources
[Practical VPC Design on Medium](https://medium.com/aws-activate-startup-blog/practical-vpc-design-8412e1a18dcc)

[A Cloud Guru's AWS Certified Solutions Architect Associate course](https://acloud.guru/)

[AWS NAT Instances docs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html)

[AWS' "What Is Amazon VPC" doc](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)

[Reference for Bastion Architecture/Configuration ](https://docs.aws.amazon.com/quickstart/latest/linux-bastion/architecture.html)

