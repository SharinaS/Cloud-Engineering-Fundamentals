This document focuses on EC2 and the associated services and technologies that come with it.

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

### Architecture Note: 
In case of failure, for production website, have at least two EC2 instances running in two separate availability zones. 

By the way, a default Security Group is set up for you, so no matter what, you start with one.

# Pricing Models
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

# Instance Types
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

----------------

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
> Note that a security group is a virtual firewall (which enables the computer to communicate via its different ports) in the cloud, so you can specify if it's open to the entire world, or only to "My IP" (only you can SSH into the webserver). For web traffic, though, you want to set it up so it can respond to web requests.

Remember:
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


### Status Checks
This is a tab that appears when you click on the instance's radio button. 

There's a system status check, which checks the underlying hypervisor, or the underlying machine. 

There's an instance status check which checks the instance itself.

### Monitoring
Involves CloudWatch analysis. 

# SSH into the Public Instance
### Via the Console:
Make sure your instance's radio button is clicked. 

Press the button, "Connect," up above. 

A window will pop up that says, "Connect To Your Instance." Click the radio button that says, "EC2 Instance Connect." Hit "Connect." A window will then open which establishes a new tab and establish a new session directly with the EC2 instance. 

Benefit is that this bypasses Mac vs Windows. 

### Via the Terminal:
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

### Remember:
* Essentially, EBS is a virtual hard disk drive in the cloud (used by EC2). Every server has a disk.
* EBS provides persistent block storage *volumes* for EC2 instances. 
* Each EBS volume is automatically replicated within its Availability Zone (providing availability and durability in case of component failure).
* You can change EBS volume sizes on the fly, including size and storage type. 
* Volumes will *always be in the same availability zone* as the EC2 instance. 
* You can *move an EC2 instance from one AZ to another* by taking a snapshot of it, creating an AMI from the snapshot, and then using the AMI to launch the EC2 instance in a new AZ.
* You can *move an EC2 instance from one region to another* by taking a snapshot of it, creating an AMI from the snapshot, copying the AMI from one region to another, then using the copied AMI to launch the EC2 instanece in the new region. 

## EBS Types
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
Snapshots exist on S3. Think of snapshots as a photograph of the disk, and that photo is stored on S3. 
* Snapshots are point in time copies of volumes.
* Snapshots are incremental, so only the blocks that have changed since your last snapshot are moved to S3. So, only the changes in the blocks are moved to S3. 
* It can take some time for snapshots to be created. 
* Best practice: when taking a snapshot for EBS volumes that serve as root devices, *stop the instance before taking the snapshot*. ... though you can take a snap while the instance is running.
* You can create AMI's from Snapshots


Go to "Volumes" on the left

Paraphrased from A Cloud Guru: Whereever the EC2 instance is, the volume will be in the same availability zone. In a physical computer, you want the hard disk drive to be as close to the motherboard as possible. In a virtual machine, and a virtual hard disk drive, you want them in the same availability zone, otherwise there will be too much lag. 

## Deletion of Volumes
After termination of an instance, the EBS volume will be automatically removed too, since delete on termination is automatically checked... since it's a root device volume. 

So, when you terminate an EC2 instance, by default the root device volume will be terminated. 

Additional volumes, attached to that EC2 instance, however, will *continue to persist.* You will have to manually click the radio buttons of each of the volumes, and click "Actions > Delete Volumes."

## Modify Volumes, Make a SnapShot, Move regions / AZs
### Example: Instance with multiple EBS volumes added
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

**Move an instance and a volume from one availability zone to another:**

> Summary: Create a snapshot --> turn the snapshot into an AMI --> Use the AMI to launch into other Availability Zones. 

* Find your root device volume (or volumes, depending on the scenario). Go to "Actions > Create Snapshot." 
  * Snapshot is basically a photograph of the disk.
* Give it a description, and click Create snapshot. It will take a few minutes to create. 
* Go to "Snapshots" on the left, to see the newly created snapshot. 
* Click on the snapshot's radio button, then "Actions > Create Image"
* Give the image a name, and leave everything else as default
  * Note there are two types of virtualization types in AWS - paravirtual (PV) or hardware virtual machine (HVM) - so you can choose which one. Make sure it's on HVM if you want to have many more instance types to launch from, later on.

Hit "Create"

Once the image has been created, we can use that image to provision new EC2 instances. 

On the left, go to "Images > AMIs" to view the newly created image, and click "Launch." 

Note how many EC2 instances are available, but you can just choose the t2.micro 

Click button, Configure Instance Details"

Can now choose to launch it into a *completely different subnet* / Availability Zone.

Click "Add Storage," where you have a Root volume. In this example, we're not adding more storage, so click "Next: Add Tags." 

In Configure Security Group, we use an existing security group, which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules.

Launch!

**Can also copy the AMI into different regions** 

> This lets us move across availability zones and/or across regions! The difference here is that we're copying the AMI from one region to another. Once the AMI is in a new region, we can choose whatever AZ that we wish. 

In the list of AMIs, with the AMI's radio button clicked, go up to "Actions > Copy AMI."

A window will pop up, and you can move the Amazon Machine Image from the existing region
* Choose a destination region

We can now use this image to launch our ec2 instances to the new destination region. 

# AMI Types
There are two different types of AMIs:
* EBS 
* Instance Store

When you select your AMI, you can select it based on:
* Region (see Regions and AZs)
* Operating System
* Architecture (32-bit vs 64-bit)
* Launch permissions
* Storage for the Root Device (aka Root Device Volume)
  * Instance Store (Ephemeral storage)
  * EBS Backed Volumes

AMIs are categorized as *one* of the following:
* Backed by EBS 
  * root device for an instance launched from the AMI is an EBS volume created from an *EBS snapshot*.
* Backed by instance store
  * root device for an instance launched from the AMI is an instance store volume created from a *template stored in S3*.
  * Sometimes called Ephemeral Storage

## Example of Launching EBS Backed vs Instance Store Backed:
### Launch an Instance that is EBS Backed
Go to EC2 and launch an instance. Choose the Amazon Linux 2 AMI, use a t2.micro instance type, leave everything as default in...
* ... the instance details config page,  
* ... add storage,
* ... add tags.

... and use a self-created security group (which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules).

Launch it. 

### Launch an Instance that is Instance Store Backed
On the left, click "Community AMIs." 
* We can sort by OS, Architecture, and by Root device type. 

In Root device type (on the left), sort by "Instance Store." 

Launch a default, perhaps called, "amzn-ami-pv-2012.03.2.x86_64-s3", though you can pick anyone you want. 

Because you are launching an instance store type, the smallest of the instance types you can choose is a medium sized one, or an m3.medium.

You can leave everything as default in:
* Configure Instance Details
* Add Storage <-- notice that volume type is "Instance Store"
  * There will be a notification on the top that you can attach extra EBS volumes after launching the instance, *but not additional instance store volumes*.
* Add Tags

... and use a self-created security group (which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules).

Launch it.

## Examine the Two Instances (launched above)
How to tell the difference between the two instances:
* the Instance Type size - m3.medium vs t2.micro

### Go to Elastic Block Store > Volumes (on left)
In the list of volumes, you'll only be able to see one volume - the EBS-backed volume.
* We can't see the instance-store volume here. 

### Persistant Storage vs Emphemoral Storage: Stop vs. Terminating the Instances
For the **EBS-backed volume**, you can go to "Actions > Instance State > Stop"
* All the data on the EBS-backed volume will continue to persist <--- *persistant storage*

For the **instance-store-backed volume**, when you go to "Actions > Instance State," the only options you have is for "Reboot" or "Terminate."
* Down below, under "Status Checks" for this volume there are two categories here (as usual) - System Status Checks and Instance Status Checks. If an underlying hypervisor (computer software, firmware or hardware that creates and runs virtual machines, aka the host) fails, the system status check will say "impaired," or something of the sort.
  * If it's an EBS-backed volume, you just stop the instance, then restart it. When it restarts, it will load up on another hypervisor. 
  * You can't do a stop/restart with the instance-store-backed volume if the underlying hypervisor has an issue. Instead, you'll have to terminate, which means *all data on the instance-store volume will be lost*. <-- *ephemeral storage*

You can reboot both EBS-backed and instance store-backed, and you won't lost your data.

### Termination
By default, both *root* volumes will be deleted on termination. 

However, with EBS volumes, you can tell AWS to keep the root device volume. 

------------------------
# ENI vs ENA vs EFA
## ENI
An Elastic Network Interface... a virtual network card for your EC2 instance, essentially.

When you provision an EC2 instance, an ENI will be automatically attached. You can then add more as needed.

ENI allows / provides:
* one primary private IPv4 address (from the IPv4 address range of your VPC)
* secondary private IPv4 addresses (from the IPv4 address range of your VPC)
* one Elastic IP address (IPv4) per private IPv4 address
* one public IPv4 address
* IPv6 addresses
* security groups
* one MAC address
* one source/destination check flag
* one description of what the ENI is.

Where an ENI is found (scenarios):
* Basic networking
* A management network separate from production network (so, multiple ENIs)
* You'd have an additional ENI if you have network and security appliances in your VPC
* Having multiple ENIs lets you create dual-homed instances with workloads / roles on distinct subnets. 
  * database subnet segregated from production subnet with multiple ENIs, for example.
  * You'd have multiple ENI's for each network, essentially.

ENIs let you create low-budget solutions that are also high-availability.


## EN 
Enhanced Networking.
* ENA is a subset of EN.
* Think of when you need speeds between 10Gbps-100Gbps and reliable, high throughput.

Uses single root I/O virtualization (SR-IOV)
* provides high-performance networking abilities on *supported instance types*. 
* SR-IOV is a method of device virtualization that essentially *speeds up your network* - it provides (compared to tranditional virtualized network interfaces):
  * higher I/O performance
  * lower CPU utilization

EN provides:
* higher bandwidth
* higher packet per second (PPS) performance
* lower inter-instance latencies

Sometimes an ENI is not enough, regarding the network throughput you need when doing more hefty workloads. Enter, enhanced networking, for when you need good network performance. So, instead of using many ENI's (which won't necessarily increase your network throughput), use one ENA. 

Cost: No additional charge

### How to enable EN - Two Options: <-- depends on your instance type
* ENA - Elastic Network Adapter
  * Supports network speeds of up to **100Gbps** (gigabits; billions of bits / second; a measure of bandwidth)
* VF - Intel 82599 Virtual Function interface
  * Supports network speeds of up to **10 Gbps**
  * Usually used for older instances, so choose ENA over VF, given the option, especially in exam questions. 

## EFA
Elastic Fabric Adapter.
* For when you need to accelerate HPC and machine learning apps, or if you need to do an OS by-pass.

A network device that you can attach to your EC2 instance.
* accelerates High Performance Computing (HPC) and machine learning applications.

If the scenario involves something regarding EN, and machine learning, think of EFA!

EFA provides (compared to the TCP transport traditionally used in cloud-based HPC systems):
* lower and more consistent latency
* higher throughput 
* OS-bypass
  * enables HPC and machine learning applications to bypass the OS kernal. Communication can occur directly with the EFA device.
  * Makes it faster with less latency.
  * Only supported on Linux currently (not windows).

-----------------------
# Encrypted Root Device Volumes & Snapshots
A root device is essentially a harddisk (the EBS) that has your OS on it. 

Nowadays, you can encrypt immediately, without the historically complex process of the past. 

Remember:
* Snapshots of encrypted volumes are encrypted automatically
* Volumes restored from encrypted snapshots are encrypted automatically
* You can share snapshots... but only if they're unencrypted
  * You can share those snapshots with other AWS accounts or made public
* You can encrypt root device volumes upon creation of the EC2 instance.

## Example
Go to EC2 and launch and instance - Linux AMI, t2.micro. 

Leave everything as default for:
* "Configure Instance Details"

In the "Add Storage" note that there is  Root (the root device volume) and it's on General Purpose SSD (gp2), with "Encryption set to "Not Encrypted."
* You can choose, within the pulldown, an option to encrypt the volume, if you decide upon creating the instance. 
* Or, you can leave it as not encrypted, and create the instance. 

For the example, re "Configure Security Group," ... use a self-created security group (which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules).

Launch. 

### Check if it's encrypted
On the left, go to "Elastic Block Store > Volumes" and click the radio button of the volume you created with the instance. 

Check the tab, "Description," for Encryption. 

### Encrypt if it was not done so upon creation:
 > make snapshot of the enencrypted root device volume --> create a copy of the snapshot and enable encryption --> make AMI from the encrypted snapshot --> launch encrypted instance(s) from that AMI. 

With the radio button clicked for the volume, go up to "Actions > Create Snapshot." 

Give it a description-name, and hit the button "Create Snapshot."

A new snapshot will be created (see "Elastic Block Store > Snapshots" on the left) and you'll see it's not encrypted.

Click the radio button for the snapshot, and go to "Actions > Copy." 

In the window that pops up, you can click the box that says, "Encrypt this snapshot." 
* It will ask for the Master Key, and you can use your default key. 
* Probably a good idea to update the description to a more readable thing.

With the radio button of the new (encrypted) copy of the snapshot clicked, go to "Actions > Create Image"
* Give it a name
* Click "Create"
* This creates an encrypted AMI.

We can now use the encrypted AMI to launch encrypted EC2 instances.
* Go to "Images > AMIs" on the left
* Launch the AMI
* default of t2.micro is good
* default values in "Configure Instance Details" are fine
* default values in "Add Storage" are fine 
  * Note that it is encrypted! You won't be able to un-encrypt it at this point - the snapshot is encrypted, so no going back now.  
* launch away for an encrypted instance. 


------------------------
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
* A Cloud Guru's [AWS Certified Cloud Practitioner](https://acloud.guru/learn/aws-certified-cloud-practitioner) Course and the AWS Certified Solutions Architect Course. 