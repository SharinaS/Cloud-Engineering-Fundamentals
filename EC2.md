# EC2
What EC2 is:
* A virtual server or servers in the cloud.
* Provides resizable copute capacity in the cloud. 
* Reduces the time needed to obtain and boot new server instances
  * Allows for quickly scaling capacity as computing requirements change
* EC2 is a compute based service; it's not serverless (it's a virtual server in the cloud). 

Note that:
* Termination protection is turned off by default, so you must turn it on if you want it. 
* The EBS-backed instance has a default action for the root EBS volume to be deleted when the instance is terminated, however non-root volumes will not be deleted.
* EBS Root volumes of your default AMI can be encrypted
  * You can also use a third party tool (bit locker, etc) to encrypt the root volume, or this can be done when creating AMI's in the AWS console or using the API.
* Added volumtes can be encrypted. 

### Architecture Note
In case of failure, for production website, have at least two EC2 instances running in two separate availability zones. 

By the way, a default Security Group is set up for you, so no matter what, you start with one.

## Pricing Models
On Demand 
* Fixed rate by the hour or second with no committment (great for testing)
* Good for low cost and flexibility, short term or unpredictable workloads that can't be interrupted. 

Reserved 
* Provides a "capacity reservation"
* 1 or 3 years term contracts
* The more you pay upfront, the more you save - significant discount.
* Good for apps with steady state or predictable usage, or for apps that require reserve capacity. 
* Further breakdown:
  * Standard Reserved Instances - up to 75% off on demand instances, the more you pay upfront and the longer the contract, the bigger the discount.
  * Convertible Reserved Instances - Up to 54% off on demand. You can convert one reserved instance type over to another. 
  * Scheduled Reserved Instances - allows you to have reserved instances within a particular time window. Great for situations like schools that have specific working hours. 

Spot
* bid a price you want to pay; when the price hits the value you bid for, you get your instance. 
* Amazon drops the price for an EC2 instance when they have excess capacity so people will use the capacity. So, the price moves around. 
* Essentially Amazon is selling off its excess capacity at a certain rate --> supply and demand pricing.
* Great for apps that are only feasible at very low compute prices and that have flexible start/stop times, and for users with urgent computing needs for large amounts of additional capacity.  
* If it's terminated by AWS, you won't pay for the rest of the hour (unlike other instances, where you pay for the full hour if you terminate prior to the hour being up)

Dedicated hosts
* physical server(s) for license terms/conditions requirements - sometimes there are requirements that may not support multi-tenant virtualization. 
* Can be purchased on-demand (hourly) and 70% off the on-demand price. 

## Instance Types
Not as important for the Solutions Architect Associate exam, but good to start learning.

Think, FIGHT DR MCPXZ AU

F1 - Field programmable gate array (FPGA)

I3 - For IOPS, high speed storage

G3 - Graphics intensive

H1 - High disk throughput

T3 - Lowest cost, general purpose

D2 - dense storage

R5 - memory optimized (Ram)

M5 - Main choice for general purpose apps

C5 - compute optimized

P3 - Pics, graphics/general purpose GPU

X1 - Extreme memory optimization

Z1D - Exteme memory and CPU (high compute capacity and high memory footprint)

A1 - Arm-based workloads (scale out workloads)

U-6tb1 - Bare Metal


# Provision an EC2 Instance 
...Using EC2's Amazon Linux 2 AMI 

![image of ec2 instance](/assets/ec2Instance.png)

## Launch a New Instance
Sign into your Amazon Console and go to EC2, and click "Launch instance."

You can click (to the left) "Free tier only"

Select the "Amazon Linux 2 AMI (HVM), SSD Volume Type" instance - amazon machine instance.

From the instance "families," choose "t2.micro" and hit "Next: Configure instance details." Note you can Filter the instance types with the filter pulldown above. 

While in the "Configure Instance Details" step, note that in the row of Network, a vpc is like a miniature datacenter in the cloud. The "Subnet" row refers to the different availability zones. "Auto-assign Public IP" refers to getting an IP address, and is auto-filled depending on what subnet is used. You can enable termination protection. Regarding tenancy, use "Shared - Run a shared hardware instance," for a basic instance.

Click "Next: Add Storage."

## Add Storage
In the "Add Storage" step, the volume type, "root," (root device volume) is our EBS instance, or the virtual hard-disk in the cloud. Every time you "Add New Volume," you get more options to choose from under the column of "Volume Type." For a simple setup, choose "Volume Type" of "General Purpose SSD (pg2)." Choose General Purpose SSD in the Root. 
* Our root device volume simply means where our operating system will be installed. 
  * Volume type for root device will only have 3 different types - General, provisioned or magnetic. 
  * IPS means input output per second. This is how fast the hard disk drive is. 
  * You can encrypt the root device volume right from the start (a new change)
* If you add a new volume, such as a EBS volume, you get two additional Volume Type options - the throughput optimized and the Cold HDD. 

Click "Next: Add Tags." 

## Add Tags
Next, we need to add key-value pairs of information about the instance by clicking on "Add Tag," such as name (of the instance), department, employee-id (of person who spun up the instance), etc. 

Then click "Next: Configure Security Group."

## Security Group
Note that a security group is a virtual firewall in the cloud, so you can specify if it's open to the entire world, or only to "My IP" (only you can SSH into the webserver). For web traffic, though, you want to set it up so it can respond to web requests.

Note that:
* Rule changes on a security group will take place immediately
* Security Grups are stateful
  * When you create an inbound rule, an outbound rule is automatically created (when you come in, you're allowed out)
  * In contrast, a network ACL is stateless, where you have to create an outbound rule intentionally.
* Note you cannot block an individual port, or a particular IP address (you have to Network ACLs), as in you cannot create deny rules.
* In a security group, all inbound traffic is blocked by default - you have to go in and manually allow traffic in, such as SSH or HTTP, or MS SQL
* All outbound traffic is allowed (because security groups are stateful)
* You can attach mutiple security groups to one instance (so, one security group can interact with certain ports).
* You can have any number of EC2 instances within a security group.

To "Configure Security Group," (a virtual firewall) make sure "Create a new security group" is chosen (radio button up top) 

Rename your "Security group name" to something identifiable and add a description, which could be the same thing you wrote in as the group name. 

For this example, choose Type "SSH," choose Source as "Custom." For CIDR range, you can type in "0.0.0.0/0"

Then, "Add Rule," and choose Type "HTTP." For Source, choose "Custom," so it too opens up to the world. CIDR range for this one can be "0.0.0.0/0, ::/0"

Hit "Review and Launch."


## Launch
In "Step 7," hit "Launch." In the box that appears, "Select an existing key pair or create a new key pair," change the dropdown to read "Create a new key pair" and give the private key pair a name. Then click "Download Key Pair" into a safe location, and Launch Instance."

![image of key value pairs](/assets/ec2KeyValue.png)

Click on "View Instances" in Launch Status page.

When you select an instance, it will reveal details about that instance, including a "Description" tag. Hover over the "IPv4 Public IP (the IP address) and click on the "Copy to clipboard" icon that appears. 

![image of part of instance description](/assets/ec2InstanceInfo.png)

# About the Instance
### Status Checks
This is a tab that appears when you click on the instance's radio button. 

There's a system status check, which checks the underlying hypervisor, or the underlying machine. 

There's an instance status check which checks the instance itself.

### Monitoring
Involves CloudWatch analysis. 

# SSH Into The Instance
### Via the Console
Make sure your instance's radio button is clicked. 

Press the button, "Connect," up above. 

A window will pop up that says, "Connect To Your Instance." Click the radio button that says, "EC2 Instance Connect." Hit "Connect." A window will then open which establishes a new tab and establish a new session directly with the EC2 instance. 

Benefit is that this bypasses Mac vs Windows. 

### Via the Terminal
For Mac users, **open up the terminal** (or a plugin for windows) and cd into where you stored your key pair. 

For first time use of the key pair, change permissions using the terminal on the private key you downloaded a bit ago: 

```
chmod 400 <name-of-key.pem>
```

Connect to the EC2 instance by typing into the terminal: 

```
ssh ec2-user@<the IP address copied in step 11> -i <name-of-key>.pem
```

Click `yes` when it asks if you're sure you want to connect. Don't worry about the first part in this simple example, when it says, "The authenticity of host.... can't be established." 

You'll see output in the terminal that says, "Amazon Linux AMI" with ascii art. 

*Note* if it times out, it's probably that your network is blocking port 22. So, *first thing* check if port 22 is open on security group. Go back the to Amazon console, to the EC2 screen, and on the left hand side go to `Network & Security` > `Security Groups`. Click on the security group name you just set up, info will appear below. Click on the tab called "Inbound rules," and check that port 22 Source column says "0.0.0.0/0," which shows it's open to the entire world. Note that a lot of networks (libraries, etc) do block port 22, so you have to get around your network restrictions to do this, like tethering to mobile phone.

Go back to the terminal, and elevate privilages to root, which allows admin access (which is easier for this beginner level): 

```
sudo su
```

Note that "root" is added to the far left in the terminal, in place of user, with a hastag instead of dollar sign to the right.

If indicated, update EC2 instances with all security patches with: 

```
yum update -y
```

## Install Apache
(Make sure you've SSH'ed into your instance first)

For if you have a public instance and you want it to be a webserver. 

```
yum install httpd -y
```

Go to your html directory. Anything you put in here, will be accessible over port 80 and will be a website!

```
cd /var/www/html
```
For a speedy example, can use nano to create a quick html file

```
nano index.html
```

## Turn on the Apache service
```
service httpd start
```
Then, to turn it on after a reboot, without having to manually turn it on:

```
chkconfig on
```

Now, can paste in public IP address in a browser, and check out the website you built. 

------------------------
# EBS Volumes
EBS = Elastic Block Storage

When creating an instance, this is "Step 4: Add Storage"

Essentially, a hard disk drive in the cloud. Provides persistent block storage volumes for EC2 instances. Each EBS volume is automatically replicated within its Availability Zone (providing availability and durability in case of component failure).

The virtual hard disks that EC2 uses. Every server has a disk, and this is also the case for virtual servers. 

Note that:
* The EC2 and the EBS has to be in the same Availability Zone.

## Types
### General Purpose (SSD)
* API Name: gp2
* For wide variety of work loads, up to 16,000 IPS/volume

### Provisioned IPS (SSD)
* API Name: io1
* Highest performance SSD volume
* Designed for mission critical 
* Great for databases
* Max IOPS is 64,000

### Throughput Optimised Hard Disk Drive
* API Name: st1
* For Big data and data warehousing
* 500 IOPS per volume

### Cold Hard Disk Drive 
* API Name: sc1
* Magnetic
* Lowest cost
* Designed for less frequently accessed work loads, like file servers
* IOPS is 250.

### EBS Magnetic
* API Name: Standard
* A Previous generation hard disk drive
* For when data is infrequently accessed, like data archiving, but where you have opted to not use glacier.

## EBS Volumes and SnapShots
Go to "Volumes" on the left

From A Cloud Guru: Whereever the EC2 instance is, the volume will be in the same availability zone. In a physical computer, you want the hard disk drive to be as close to the motherboard as possible. In a virtual machine, and a virtual hard disk drive, you want them in the same availability zone, otherwise there will be too much lag. 

## Deletion of Volumes
After termination of an instance, the EBS volume will be automatically removed too, since delete on termination is automatically checked.

### Example: instance with multiple EBS volumes added
Amazon Linux AMI --> T2.micro instance

Everything left as default in the Configure instance details. 

Add Storage:
* EBS, General Purpose
* EBS, Cold HDD 
* EBS, Throughput Optimized HDD
* EBS, Magnetic
* All have default sizes and no encryption. Delete on termination kept the way it is by default. 

Configure Security Group:
* Select existing security group
  * Inbound HTTP with 0.0.0.0/0, HTTP with ::/0, SSH with 0.0.0.0/0

Review and launch. 

Can modify a volume
* Click on volume's radio button, and up in Actions, click "Modify Volume." 
* You can go in and change the size. 
  * change the throughput optimized HDD (st1) from 500 --> 1000 gigs.
* You can go in and change the type
  * change the gp2 type to Provisioned IOPS SSD (io1)
* It can change some time for the changes to take effect. May also need to extend the OS file system on the volume - you run a command to repartition the drive to see the full 1000 gigs (in sys ops course).
* Note that you don't have to stop an instance to change things about volumes!

Move an instance and a volume from one availability zone to another:
* Find your root device volume (or volumes, depending on the scenario). Go to "Actions > Create Snapshot." 
  * Snapshot is basically a photograph of the disk.
* Give it a description, and click Create snapshot. It will take a few minutes to create. 
* Go to "Snapshots" on the left, to see the newly created snapshot. 
* Click on the snapshot's radio button, then "Actions > Create Image"
* Give the image a name, and leave everything else as default
  * Note there are two types of virtualization types in AWS - paravirtual (PV) or hardware virtual machine (HVM) - so you can choose which one. Make sure it's on HVM if you want to have more instance types to launch from later on.


------------------------
# Firewalls
Firewalls enable the computer to communicate via its different ports. 

------------------------

# Use the Terminal (Mac) To interact with AWS
## Gain Access to Your EC2 Instance
Make sure there is a User added within IAM on the Amazon console with Administrator Access. 

If you're using credentials (don't; a role is much more secure):
* You should have an Access key ID and a Secret access key downloaded somewhere (regenerate these by going to `IAM` > `Users` > the user name > `Security Credentials` tab, and under the "Access keys" section click `Make inactive` or `Delete`... and then click `Create access key` to generate a new set of keys. Make sure to download the **.csv file!**).

## Head over to the terminal. 
In the Amazon console, go to EC2 > Running Instances. Click the box next to the instance name, and click on Description tab. Copy to clipboard the "IPv4 Public IP." Also make note of the "Key pair name" in this section.

Navigate to the folder you downloaded (or moved) the **.pem** file into and type into the terminal:

```
ssh ec2-user@<IPv4 Public IP> -i <your Key pair name>.pem
```

You'll see output like this appear:

![screenshot of terminal after acessing key](/assets/linux2ascii.png) 

(3) Elevate your priviledges to root with:
```
sudo su
```
So, your terminal will have something like as its path:
```
[root@ip-111-11-11-11 ec2-user]#
```
-------------------

# Configure the EC2 Instance to Interact with S3 (or other AWS Services)
We need to configure the EC2 instance otherwise the terminal will complain that it's unable to locate credentials when you do something like try to make an S3 bucket. 

Can either give it **credentials** of the administrator that was set up above, or you can do it by using **roles**. 

### To Use Credentials:
*Note that using the command line as demoed in this section leaves port 22 completely open... so it could be hacked... so the access keys can be easily found and used to gain control of the AWS account. Other option - use roles to increase security! (see below).*


Note that the directory you're in should look like (see above for steps into this thing):

```
[root@ip-111-11-11-11 ec2-user]#
```
Tell AWS you want to dos some configuring:
```
aws configure
```
When it asks for the Access Key and the Secret Access Key, copy paste from the .csv file you downloaded (when setting up your terminal above) into the terminal when prompted. 

It'll then ask for the region name. Type in the region you usually work in, such as:
```
us-west-2
```
Hit enter when it asks for output format to choose default. 

Now you can make an S3 bucket or do whatever else you want.

## To Make and Use a Role:
This is more secure than using credentials. No .aws file is generated, and so no access is available to snatch up public or private keys. 

See [my notes in IAM.md on making a role]().

# Check Configuration and Credentials from the Terminal
Make sure you have access to your EC2 instance via the terminal 

## Grab the instance's Public IP and Key pair name
In the Amazon console, go to EC2 > Running Instances. Click the box next to the instance name, and click on Description tab. Copy to clipboard the "IPv4 Public IP." Also make note of the "Key pair name" in this section.

## SSH Into the EC2 Instance
Navigate to the folder you downloaded (or moved) the **.pem** file into and type into the terminal:

```
ssh ec2-user@<IPv4 Public IP> -i <your Key pair name>.pem
```
```
sudo su
```

You'll be in a directory that looks like:  

```
[root@ip-111-11-11-11 ec2-user]#
```

Go to the home directory:
```
cd ~
```
### If you set up *Credentials*, there *will* be a **.aws** file 
get into the hidden aws file, which will have config and credentials
```
cd .aws
```
Since credentials is a file, use nano (text editor for linux) to view it and the access keys within it:
```
nano credentials
```
### If you set up a *Role* via IAM, you *don't want* a **.aws** file
Remove the .aws file
```
rm -rf .aws
```
Ask it to try and connect to S3 - to list the buckets that exist - using the role you set up ([see notes here]() on doing this), now that .aws is gone:
```
aws s3 ls
```

# Build a Simple Web Server
[Go to my file on building a simple web server](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/BUILD_WEB_SERVER.md), using Apache and an EC2 Instance. 


# Resources
* A Cloud Guru's [AWS Certified Cloud Practitioner](https://acloud.guru/learn/aws-certified-cloud-practitioner) Course