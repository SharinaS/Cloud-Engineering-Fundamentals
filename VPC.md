Should be able to build a VPC from memory to pass the AWS Certified Solutions Architect Exam.

# What is a VPC
A virtual data center in the cloud.

A VPC is a *collection* of internet gateways, route tables, network access control lists (ACLs), security groups, EC2 instances, private and public subnets.
* VPC consists of IGWs(Virtual Private Gateways)

Amazon VPC lets you provision a virtual networking environment. You can fully control it, and customize it. You can have security groups and network access control lists to help control acces to AWS EC2 instances in each subnet. 

With a VPC, we can:
* Launch instances into a subnet of my choosing
* Assign custom IP address ranges in each subnet
* Configure route tables between subnets
* Create internet gateway and attach it to our VPC
* Have much better security control over your AWS resources
* Assign security groups to individual instances
* Have Subnet network access control lists (ACLS)

### Default VPC:
* User friendly
* Can immediately deploy instances 
* All subnets have a route out to the internet - they're all internet accessible.

Each EC2 instance has a public and private IP address.

### Examples
Example: A public-facing subnet for your webservers, with backend systems like a DB placed in a private-facing subnet. 

Example: A Hardware Virtual Private Network (VPN) connection between a corporate datacenter and your VPC. The AWS cloud becomes an extension of your corporate datacenter. 

### Subnets
1 subnet = 1 availability zone.

You can't have 1 subnet spread across multiple availability zones.

10.0.0.0/16
* This is the largest subnet you can use within the VPC, and the most commonly used. 

10.0.0.0/28
* This is the smallest subnet you can use within a VPC. 

### VPC Peering
Allows you to connect one VPC with another via a direct network route using private IP addresses. 

Instances act as though they are on the same private network.

Can peer VPCs with other AWS accounts, and with other VPCs in the same account.
* Peering is in a star configuration (1 central VPC peers with 4 others).
* Can peer across regions

### Transitive Peering
Instances within one VPC can talk to the instances within a connected VPC. For instances to talk to each other, you have to directly connect a VPC to another VPC. You can't talk to an instance in secondarily connected VPCs. 

In other words, there is *no transitive peering!*

### Security Groups
These are *stateful*.

### Network Access Control Lists (ACLs)
These are *stateless*. 

You can add deny and allow rules. When you open a port on inbound, it doesn't mean a port opens on outbound. 

### Availability Zones... when it comes to this stuff:
An availability zone in my AWS account can be a totally different availability zone from the one found in another AWS account, even though they may be called the same (ie, US-East-1A). This is because they are randomized when you choose them in the following steps. 

# Create a Custom VPC - VPC with Public & Private Subnets
"Create VPC"

Name it.

IPv4 CIDR address block:
For the biggest block possible: `10.0.0.0/16`

IPv6 CIDR block: Can use the `Amazon provided IPv6 CIDR block` - radio button.

Tenancy: `default`
* Less expensive - you're sharing your underlying hardware with other AWS customers.
* Dedicated will cost a fortune.

Hit "Create"

***When we create a custom VPC, it creates 3 things:***  
* A default Route Table
* A Network Access Control List (NACL)
* A default Security Group is created (can identify it with the VPC ID)

***2 Things are NOT created***
* an Internet Gateway
* Subnet(s)


*To use the VPC we need to create some subnets.*

## Create Subnets
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

Scroll to the right in list of Subnets. The Auto-assign public IP Address has been turned *off*. We'll need to turn this on, since I want one of subnets we just created to be private and the other public. 

Also note that we only have 251 available IP addresses. We should be getting 256 (per the CIDR.xyz website), with a usable amount of 254.
* [See VPC and Subnet Sizing](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#VPC_Sizing)
  * There will always be 5 addresses that are reserved for other uses, which explains our 251 vs 256. 

## Make a Subnet Publicly Accessible
For a subnet to be publically accessible, we'll need *EC2 instances* to launch in it with public IP addresses. 

Select the subnet you want to make public - the radio button. 

Go to the tab that says "Actions," which produces a dropdown menu when you click on it. Click the "Modify auto-assign IP settings" option.

On the page that opens (which also says: "Enable the auto-assign IP address setting to automatically request a public IPv4 or IPv6 address for an instance launched in this subnet. You can override the auto-assign IP settings for an instance at launch time.")... 

... click the box that says "Enable auto-assign public IPv4 address."*

Hit "Save"

The chosen subnet, within the list of subnets will show a "Yes" under the column of: "Auto-assign public IPv4 address." 

*This now lets us launch EC2 instances into this particular subnet, and that automatically allows for a public IP addresss :)*

Now, we have a public and private subnet, 
![screenshot of current VPC architecture](/assets/subnetVPC.png)

Next, we need an **Internet Gateway**, which gives us a way to get into the VPC. Then, we'll need to configure our route tables. 

## Add an Internet Gateway
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
Make a route out into the Internet.

Go to Routes Table. 

Click on the radio button that corresponds to the route that belongs to the VPC you created. Check the column "VPC ID" if you're not sure (there will be a default route in there too)

Down below, click the tab "Routes." You'll see two routes. These two routes are letting the two subnets I created "talk to each other."

Click the tab "Subnet Associations." You'll see a note that says "You do not have any subnet associations." So, if we put a route out into the internet via our main route table, any subnet we create will be public by default. We always want our main route *private.* We want a *separate route table for our public subnets.*

Go back to the tab "Routes." Click "Create Route Table." A new page will open up. Name it and choose the VPC you want to associate it with. 
* I'm calling this one "Sharina-PublicRoute."
  * Any subnet associated with this route table will be able to talk to the internet.

Back in the list of routes, I now have two routes - the original one that got created with the VPC creation, and this new one. Note that the automatically made one is the main route. 

### Make routes for IPv4 and IPv6 to the Internet Gateway:
With the radio button clicked for the PublicRoute you created, next, to create a route out to the internet, Click the tab, "Routes", and then the button, "Edit routes." In the new screen, click " Add route. A new row is created. Add in `0.0.0.0/0`. For Target, choose "Internet Gateway," and choose the gateway you created. 

We can now add our route, by adding another row to the "Edit routes" table.  We can add the IPv6 by typing in `::/0` to give us a route out. In Target, choose "Internet Gateway" again, and choose the gateway created prior.

Now, any subnet associated with the Route Table will automatically become public (based on the above settings) for IPv4 and IPv6. 

### Associate the Subnets
Given that my radio button is still clicked for "Sharina-PublicRoute," I'm going to click the tab "Subnet Associations." Then, click the button, "Edit subnet associations.

Out of the two subnets that appear, click the radio button of the one you'd like to associate with your route table. 

The other subnet will then be associated will the main route table (the automatically created one).

The 0.0.0.0/0 will now point to the internet gateway. 

Now, we have a private subnet and a public subnet.

Next, we need to *provision EC2 instances* - one in the public subnet and one in the private subnet.

## Provision EC2 Instances
Go to EC2 service. Launch an instance, and choose the "Amazon Linux 2 AMI." Make sure the t2.micro is chosen, and hit "Next: configure Instance Details."

In the page that opens, chose the VPC you created way back when. Choose the subnet you decided the public one. 

Make sure the "Auto-assign Public IP" drop down says "Use subnet setting (Enable). If it doesn't, you chose the wrong Subnet in the dropdown just above. 

### Click "Next: Add Storage." Nothing to do here, so Click "Next: Add Tags" 

Add a tag to help identify the instance. 

### Click "Next: Configure Security Group"
Note that security groups do not span VPCs.

In the "Assign a security group" options, choose the radio button that says, "Create a new security group." Give it a name and Description. 

Click "Add Rule" and because we want to give it HTTP access, choose the Type to be HTTP for the new rule. Meanwhile, keep the SSH type the way it is (just above the new Rule you just created).

Click "Review Instance and Launch" button.

This will create an HTTP instance that will allow SSH and HTTP to be open to the world. 

Then, click "Launch" button.

A window will pop up that tells you to choose or create a key pair. If you make a new key pair, remember to download the key pair. 

Click "Launch Instance"

### Now, go back to instances, and launch a second instance. 
For this instance, follow the same steps as above, except when you get to Configure Instance Details, we want to choose the subnet that produces an "Auto-assign Public IP" statement that says *"Use subnet setting (Disable)"*. This is because for this one, we are *not* wanting a public IP address for this EC2 instance. 

This is because we are going to create a database server, which doesn't need to be public. 

So, for the "Add Tags" section, give it a key of Name and something descriptive. 

For the "Configure Security Group Section," it's fine to "Select an existing security group." Choose the "default VPC security group."

When the box pops up that says "Select an existing key pair or create a new key pair," choose the keypair you created prior (at least for the last instance). 


What we've got so far:
![Screenshot of a cloud guru lesson showing VPC with Public and Private Subnets](/assets/vpc-with-two-subnets.png)

## Test the Public EC2 Instance (the Web Server)

Via the terminal, navigate to where you stored your KeyPair. 

If it's a new key pair, you'll have to establish the appropriate permissions with a chmod command:

```
chmod 400 SharinaKeyPair.pem 
```

Grab the IPv4 Public IP value (the IP address) of the public instance you made. 

To SSH into the instance, type into the terminal the following, plus the IP address, plus the file name for the pem file. 

```
ssh ec2-user@the-ip-address -i SharinaKeyPair.pem
```
Elevate priviledges to root:

```
sudo su
```

*Now, need to examine the private instance.*

## Access the Private Instance (the Database Server):
*The private one does not have an IP address to connect to, since it's private*

Note that the two instances have two separate security groups, and so, by default, the security groups do not allow access to each other. So, if you try to SSH into the instance that is private (the DBServer), via the instance that is public (the WebServer), it's not going to work. 

### Solution: Create another Security Group within the Default VPC for the database server
* *It's also helpful to have a security group for the private instance, anyway, since it is going to be a database server :)*

Go to EC2. 

Scroll down on the left hand side to "Network and Security," and click on "Security Groups."

Click "Create security group" near the top of the page where security groups are listed.

Name it with something descriptive (mine is, Sharina-Database-Security-Group-us-west-2).

Give it a description and associate it with the VPC you made way, way back when for this project, such as: "Security Group for the database server built within sharinaVPC"

Click "Add rule" within the "Inbound rules section."
* This is where we get to communicate certain things to EC2 instances from within this security group.
* We want to ping EC2 instances inside this security group from within our WebServer's security group, so for Type, choose, "All ICMP - IPv4." So the source will be our web security group, or the IP range.
  * For Source type, leave it as Custom
  * For Source, this is where we allow the web server's security group to ping, so you can type in "sg" or start searching for the security group you want to have as the source (the other security group we created a while back). Or, can just type in what the IPv4 CIDR is, which is the IP address range. (Go to Subnets (within VPC service) to check what the IPv4 CIDR is. This will be for the subnet that is associated with this particular instance. In this project's case, this is for my private subnet, which I named, "sharina-10.0.1.0-private-us-west-2a."). 
* We want to talk to our database servers using HTTP, such as when we install managment software to manage mySQL, so add another rule.
  * For Type, choose "HTTP." For Source, type in the IP address range. In my case, it's "10.0.1.0/24" 
* We want HTTPS, so choose this in Type, and add in the IP address range in Source.
* We also want SSH, so choose this in Type, and add in the IP address range in Source.
* We need MySQL/Aurora, which allows us to communicate to DB server using MySQL. Do the same for Source. 

Leave the outbound rules the way they are - it should have Type "All traffic," and have destination "0.0.0.0/0", "::/0"

Click "Create Security Group"

![screenshot of inbound rules for this security group](/assets/dbserversecuritygroup.png)

**Now, we need to move the database server we created into this new security group:**

So, we created the new security group which is within the default VPC.

We now need to move the database server. 

Go to EC2. Click on the radio button for the database server you created a while back.

Up above, click the pulldown menu called "Actions," and choose "Change Security Groups."

A new window will pop up, called "Change Security Groups." You'll see that the default security group is currently chosen. Unclick it, and click your newly created security group (in my case, the super long title of "Sharina-Database-Security-Group-us-west-2." Seriously, who's idea was that?). Hit the button to confirm all that.

Back in the list of EC2 instances, with your radio button still checked, go down to the tab called "Description," and copy to clipboard the private IP address, titled "Private IPs."

**We now get to SSH back into our public web server!**
Via the terminal, navigate to where you stored your KeyPair. 

```
ssh ec2-user@the-ip-address -i SharinaKeyPair.pem
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
ping private-ip-address-of-database-server 
```
-------------
# NAT Instances and Gateways
nat = network address translation

In a private subnet, How to install updates? Install updates? Download software? Must use NAT Instances & Nat Gateways, since there is not route set up to the Internet! These things make it possible to communicate with our Internet Gateway, without making our private subnet public.

In other words, when our instances in our private subnets need to access the internet, they do so via either a NAT Instance or a NAT gateway. 

Nat Gateways are used 99% of the time; Nat Instances are *individual EC2 instances* that do this. Gateways are *highly available, spread across multiple AZ's*. Instances are on their way out... but they're still on the exam.

Problems with NAT Instance architecture:
* It is a single virtual machine
  * Can create high availability using Autoscaling groups, multiple subnets in different AZs, and a script to automate failover... but ouch. 
* It's a small virtual machine, so it can easily get overwhelmed - it's a massive bottleneck.
  * Increase the instance size to deal with bottle-necking.
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
* Changed the vpc to my custom vpc.
* Change the subnet to the public subnet (want to launch into the public one), which will give us a public ip address, regarding the "Auto-assign Public IP" - it will say "Enable"
* Leave everything else the way it is. 

Click the button that says "Add Storage," where you do nothing, just click the button to "Add tags."

Add a few descriptive tags, definitely at least a name. 

Click "Configure security group."

Choose the "Select an existing security group" radio button so we can put this instance inside our web server security group (ie, not the database security group created a while back). 

Click "Review and launch."

A window will pop up that asks where to boot from, re SSD. Choose the radio button that says, "Make General Purpose (SSD) the boot volume for this instance." If you don't get this window, it's probably b/c of the AMI you're using. 

Review the instance, and click "Launch"

A window will pop up asking about key pairs - choose the key pair you've used above. ... and click "Launch instances"

### Make it a "Gateway" of sorts by Disabling Source/Destination checks

Click on the NAT instance radio button. Go to the Actions tab, and choose "Networking > Enable Source/Destination Check." Go ahead and disable it. 

### Make the Database Server "talk" to the NAT Instance
To do this, we need to create a route out to the internet. The route needs to be created in the default route table, which is the main route table. 

Go to VPC (within network and content delivery), and Go to "Route Tables," on the left, within the Virtual Private Cloud section. 

click the radio button of the route table that is associated with your VPC, and says "Yes," in the "Main" column. This is the main route table associated with your VPC. 

In the tabs below, click "Routes." 

Click "Edit routes," and then "Add route." 

If we want to go out to the internet, we add "0.0.0.0/0," as our Destination. We then add our NAT instance as the Target, by clicking "Instance" on the pulldown menu and then choosing the NAT instance we created above.

Click "Save routes."

Now we have a route within our main route table, which says, go over this elastic network interface (eni-).

### Terminate the Instance, since, seriously, so many issues with this architecture!
Click the radio button for the nat instance. Click the Actions pulldown menu above. Choose "Instance State > Terminate"

Remember to remove the route associated with this instance, too. Go to VPC > Route Tables.

Click on your main route table, click the tab below that says "Routes." You'll see one that says, blackhole! This is because the instance no longer exists. 

Go to "Edit routes," and click on the "x" to the right of the route, then click "Save routes."

## Create a NAT Gateway - scaleable and highly available
Things to Note:
* They are redundant inside the availability zone.
* You can only have one NAT gateway in an AZ.
  * To create AZ-independant architecture, you must create a NAT gateway in *each* availability zone, and configure your routing to ensure that resources use the NAT gateway in the same AZ that they're in. 
* Preferred by the enterprise
* It scales automatically
* No need to patch the OS 
* Not associated with any security groups! (Doesn't need to be behind one)
* Automatically assigned a public ip address
* No need to disable source/destination checks

### Make a Gateway
Go to VPC > Vitual Private Cloud > NAT Gateways.

Click the button, "Create NAT Gateway."

You'll want to choose your *public* subnet that you created way in the beginning. 

Click the button that says "Allocate Elastic IP Address," which will create an IP address and attach it to the new Gateway. 

Create the Gateway.

### Edit route tables
A window will pop up saying you'll need to edit your route tables. Click on the "Edit route tables" button.

You'll find yourself at the list of Route Tables.

Click the radio button for your *main* route table inside your VPC. Click the tab called "Routes" and then the button "Edit routes."

We need to give the main route table a route out into the internet. Click "Add route," and put the destination as 0.0.0.0/0, and change the Target to NAT_gateway, and choose the one you created. 

Click the "save" and "close" button.

From A Cloud Guru... An example of how to diagram what we might have so far. Note that what *I* created is a bit different.
![screenshot of what we've got so far](/assets/vpcwithnatgateway.png)

---------------
# Network Access Control Lists (NACL)
"A network ACL is an optional layer of security that acts as a firewall for controlling traffic in and out of a subnet," says AWS.

Often a good thing to make sure there are not too many of, if not none, paraphrased from an individual at 1Strategy. 

Go to VPC > Security (to the left) > Network ACLs.

You'll see a list of Access Control Lists. Two were created at some point along the journey - one with 2 subnets (sits inside our custom VPC), and one with 3 subnets (sits within the default VPC). 

In other words, when we created the custom VPC, a network ACL was created by default. By default, it allows all outbound and inbound traffic. 

Everytime we add a subnet to our VPC, it will be associated with our default Network ACL. In fact, it *must* be associated with a network ACL - if you don't explicitly associate a subnet with a netowrk ACL, the subnet will be automatically associated with the default network ACL. 

Note, we can associate a subnet with a new NACL, but *a subnet itself can only be associated with one network ACL at a given time*. Network ACLs can have multiple subnets on them, however. 

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

*Once a new NACL is created, note that all inbound and outbound rules are automatically set to deny everything*.

### Change Subnet Associations
Click on the new NACL's radio tab, and click on the tab below, calld "Subnet associations."

Click "Edit subnet associations"

Choose the subnet you want - I chose my public one. 

The subnet is now associated with this new NACL, and the other subnet is left behind in the other one. 

Now that the NACL has the public subnet it in, any access via HTTP is no longer available (ie, Apache setup would have shown ability to see a little website via IP address in browser - see AWS_CLI_COMMANDS.md). 

### Set up Rules for new NACL
Click tab, "Inbound rules," then "Edit inbound rules"
* Can create rules to connect on port 80, 443, and 22, for example, inbound.

Edit Outbound rules
* connect on port 80, 443, 1024-65535 (ephemoral ports for NAT Gateway)
  * short lived transport protrocol port is an ephemeral port. They may be used on the server side of communication. Port only available during the communication, then times out. NAT gateway uses the emphemoral port of the type noted above. 

The rules are evaluated in order - 100 (HTTP), 200 (HTTPS), 300 (SSH) for inbound rules. So, if there's a deny rule setup as rule 400, the deny won't activate till everything else is done. If you set up a deny rule for a specific IP address, the deny rule needs to be numbered 99, in this case, so it's before the allow rules. 

----------------
# Custom VPCs and Elastic Load Balancers
When you provision a load balancer, you'll need *at least two* public subnets. Otherwise, it won't let you create a load balancer. 

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
  * traffic generated by instances when te ycontact the Amazon DNS server is not monitored, however if you use your own DNS server, all traffic to that DNS server is logged.
  * Traffic to/from 169.254.169.254 for instance metadata not monitored
  * DHCP traffic not monitored
  * traffic to the reserved IP address for the default VPC router also not monitored.

-----------------
# Bastion Host
A special purpose computer set up to resist attacks. It's usually on the outside of a firewall or public subnets. Usually involves access from untrusted networks or computers. It is used to securely administer EC2 instances (usign SSH or RDP).

Note that a NAT Gateway or NAT Instance is used to provide internet traffic to EC2 instances in a private subnet. 

If we have a public subnet, the bastion host would go inside the public subnet. SSH or RDP through the Internet Gateway, through our Route Tables, through the Network ACLs, Through Security Groups, onto the Bastion server. The Bastion server would then forward the connection through SSH or through RDP to our private instances. So, we just harden the Bastion host (since this is what will be hacked). That way we don't need to harden our private instances. 

Note that you can't use a NAT Gateway as a Bastion host; you have to go ahead an configure a bastion host. 

-----------------
# Direct Connect
A direct connection into AWS, using dedicated lines. It is an AWS service. 

Allows for private connectivity between AWS and a datacenter, office, etc. 

Direct connect locations are spread all over the world. 

Within direct connect, is a "AWS cage", which has routers. Customers routers then connect to the routers within the AWS cage. None of this traverses the internet. There is use of the AWS backbone network. 

Useful for high throughput workloads (ie lots of network traffic). 

Useful for when you need a stable and reliable and secure connection. For example, if you have situation where you're trying to use a VPN connection but it keeps dropping out b/c of the amount of throughput, you can use a direct connect to solve those issues. 

## Steps for Setup
Note that if one does not have a direct connect connection at your location, one can't do these steps. 

1. Create a virtual interface in the Direct Connect console - this is a Public Virtual Interface.
2. Go to the VPC console > VPN connections. Create a Customer Gateway.
3. Create a Virtual Private Gateway
4. Attach the Virtual Private Gateway to the desired VPC.
5. Select VPN Connections and create new VPN Connection
6. Select the Virtual Private Gateway and the Customer Gateway
7. Once the VPN is available, set up the VPN on the customer gateway or firewall.

^-- memorize these steps for the exam and watch this video on YouTube, called [How do I configure a VPN over AWS Direct Connect?](https://www.youtube.com/watch?v=dhpTTT6V1So). 

-----------------
# Global Accelerator
A service in which you create acclerators to improve availability and performance of your app for local and global users. Global Accelerator directs traffic to optimal endpoints over the AWS global network. Much more efficient. 

GA gives you *two static IP addresses* that you associate with your accelerator. You can also bring your own IP addresses. 

User --> Edge location --> AWS Global Accelerator --> Endpoint group --> Endpoints

You can control traffic using traffic dials, so it's very highly configurable.

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
Allows traffic between your VPC and the other service to not leave the Amazon network. It's a private connection between your VPC to supported AWS services a

-----------------
# Learning Moments
Hardest part to building a VPC, when doing this for the first time, is making sure that one is working within or with the correct subnet, given the goals for that subnet. So, labeling each subnet descriptively is really important. Actually, label *everything* descriptively. I didn't at first, and that got me in a bit of confused trouble at times. 



-----------------

# Resources
[Practical VPC Design on Medium](https://medium.com/aws-activate-startup-blog/practical-vpc-design-8412e1a18dcc)

[A Cloud Guru's AWS Certified Solutions Architect Associate course](https://acloud.guru/)

[1Strategy AWS VPC starter CloudFormation template](https://github.com/1Strategy/vpc-starter-template)

[AWS NAT Instances docs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html)