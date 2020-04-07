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
![Screenshot of a cloud guru lesson showing VPC with Public and Private Subnets](vpc-with-two-subnets.png)

## Test the Things 

### Connect to the Public EC2 Instance

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

### Check the Private One:
*The private one does not have an IP address to connect to, since it's private*

Note that the two instances have two separate security groups, and by default, the two security groups do not allow access to each other. 

### Solution: Create another Security Group
*Helpful, since this instance is for a database, to have its own security group :)*

Go to EC2. 

Scroll down on the left hand side to "Security Groups"

# Resources
[Practical VPC Design on Medium](https://medium.com/aws-activate-startup-blog/practical-vpc-design-8412e1a18dcc)