# Cloud Engineering Fundamentals - AWS
A repository dedicated to the learning of cloud engineering, specifically AWS Services. This project started after earning my certificate in "Advanced Software Development in Java" at Code Fellows in Seattle, and as such also includes links to notes I took during that academic time period. 

Do note that a number of these technologies, such as Amplify, have changed versions since I did projects using them, so, check the docs carefully when applicable, and switch to the most up to date version. The amplify docs are a great example - some of my notes may still reference amplify 1.0; at the time of writing this, you actually want to make sure you link over to amplify 2.0 version of the docs, otherwise I can just about guarantee your project won't run properly (yes, that's experience talking, unfortunately). 

# Amplify 
You may find my notes on integrating Amplify with Android, along with a number of AWS services, Amplify and Android in my [Java Fundamentals wiki](https://github.com/SharinaS/java-fundamentals/wiki). Specific topics associated with Amplify and Android - with links to this wiki - are found below:
* [Amplify and Android](https://github.com/SharinaS/java-fundamentals/wiki/Android-AWS-Amplify-and-Android)
* [API](https://github.com/SharinaS/java-fundamentals/wiki/Android-AWS-Amplify-and-Database-Setup-with-GraphQL-API)
* [Authentication](https://github.com/SharinaS/java-fundamentals/wiki/Android-Authentication)
* [Cognito](https://github.com/SharinaS/java-fundamentals/wiki/Android-AWS-Cognito)
* [Database and GraphQL](https://github.com/SharinaS/java-fundamentals/wiki/Android-AWS-Amplify-and-Database-Setup-with-GraphQL-API)
* [Kinesis and Pinpoint](https://github.com/SharinaS/java-fundamentals/wiki/Android-Amazon-PinPoint-and-Kinesis,-and-Experiments), and more on [Pinpoint](https://github.com/SharinaS/java-fundamentals/wiki/Android-Notifications)
* [Location Services](https://github.com/SharinaS/java-fundamentals/wiki/Android-Location)
* [Subscriptions](https://github.com/SharinaS/java-fundamentals/wiki/Android-Subscriptions)
* [S3 and File Uploads](https://github.com/SharinaS/java-fundamentals/wiki/Android-Amplify-S3-and-File-Uploads)

# EC2
* A virtual server or servers in the cloud.
* Reduces the time needed to obtain and boot new server instances
  * Allows for quickly scaling capacity as computing requirements change
* A default Security Group is set up for you, so no matter what, you start with one.
* EC2 is a compute based service; it's not serverless (it's a virtual server in the cloud). 
* To connect, you need a private key. 

### Architecture Note
In case of failure, for production website, have at least two EC2 instances running in two separate availability zones. 

### Pricing Models
* On Demand
* Reserved - 1 or 3 years term contracts
* Spot - bid a price you want to pay
* Dedicated hosts - physical server(s) for license terms/conditions

## EBS
* The virtual hard disks that EC2 uses. 
  * Every server has a disk, and this is also the case for virtual servers. 
  * The EC2 and the EBS has to be in the same Availability Zone.
* There's SSD (2 types)
  * GP2
  * IO1
* ... and there's Magnetic (2 types)
  * ST1
  * SC1
* ... and the previous generation is called magnetic

## Provision an EC2 Instance Using EC2's Amazon Linux 2 AMI 
![image of ec2 instance](/assets/ec2Instance.png)

### Using the Amazon Console:
1. Sign into your Amazon Console and go to EC2, and click `Launch instance`.
2. Click (to the left) `Free tier only`
3. Select the `Amazon Linux 2 AMI (HVM), SSD Volume Type` instance
4. From the instance "families," choose `t2.micro` and hit `Next: Configure instance details`. 
5. While in the "Configure Instance Details" step, note that in the row of Network, a vpc is like a miniature datacenter in the cloud. The "Subnet" row refers to the different availability zones, while the "Auto-assign Public IP" refers to getting an IP address. Click `Next: Add Storage`.
6. In the "Add Storage" step, the volume type, "root," is our EBS instance, or the virtual hard-disk in the cloud. Every time you `Add New Volume`, you get more options to choose from under the column of "Volume Type." For a simple setup, choose "Volume Type" of `General Purpose SSD (pg2)` and then click `Next: Add Tags`. 
7. Next, we need to add key-value pairs of information about the instance by clicking on `Add Tag`, such as name (of the instance), department, employee-id (of person who spun up the instance), etc. Then click `Next: Configure Security Group`.
8. To "Configure Security Group," make sure `Create a new security group` is toggled. Rename your "Security group name" to something identifiable and add a description, which could be the same thing you wrote in as the group name. Note that a security group is a virtual firewall in the cloud, so you can specify if it's open to the entire world, or only to `My IP` (only you can SSH into the webserver). For web traffic, though, you want to set it up so it can respond to web requests... so for this example, for type `SSH`, choose `Anywhere`. Then, `Add Rule`, and for port type `HTTP`, choose `Custom`, so it too opens up to the world. Hit `Review and Launch`. 
9. In "Step 7," hit `Launch`. In the box that appears, "Select an existing key pair or create a new key pair," change the dropdown to read `Create a new key pair` and give the private key pair a name. Then click `Download Key Pair` into your downloads directory, and `Launch Instance`.
![image of key value pairs](/assets/ec2KeyValue.png)

10. Click on `View Instances` in Launch Status page.

11. When you select an instance, it will reveal details about that instance, including a "Description" tag. Hover over the "IPv4 Public IP (the IP address) and click on the `Copy to clipboard` icon that appears. 
![image of part of instance description](/assets/ec2InstanceInfo.png)

12. For Mac users, **open up the terminal** and cd into Downloads - `cd Downloads` (PC users, sorry, haven't stepped through this process yet on Windows). 
13. Change permissions using the terminal on the private key you downloaded a bit ago: `chmod 400 <name-of-key.pem>`

14. Connect to the EC2 instance by typing into the terminal: `ssh ec2-user@<the IP address copied in step 11> -i <name-of-key>.pem` and hit `yes` when it asks if you're sure you want to connect. Don't worry about the first part in this simple example, when it says, "The authenticity of host.... can't be established." 

15. You'll see output in the terminal that says, "Amazon Linux AMI" with ascii art. 

16. But, it might have timed out, so *first thing* check if port 22 is open on security group. Go back the to Amazon console, to the EC2 screen, and on the left hand side go to `Network & Security` > `Security Groups`. Click on the security group name you just set up, info will appear below. Click on the tab called "Inbound rules," and check that port 22 Source column says "0.0.0.0/0," which shows it's open to the entire world. Note that a lot of networks (libraries, etc) do block port 22, so you have to get around your network restrictions to do this, like tethering to mobile phone.

17. Go back to the terminal, and elevate privilages to root, which allows admin access: `sudo su`. Note that "root" is added to the far left in the terminal, in place of user, with a hastag instead of dollar sign to the right.

18. Update EC2 instances with all security patches with: `yum update -y`.

### Firewalls
Firewalls enable the computer to communicate via its different ports. 

## Use the Terminal (Mac) To interact with AWS
### First, Gain Access to Your EC2 Instance
(1) Make sure there is a User added within IAM on the Amazon console with Administrator Access. 

You should have an Access key ID and a Secret access key downloaded somewhere (regenerate these by going to `IAM` > `Users` > the user name > `Security Credentials` tab, and under the "Access keys" section click `Make inactive` or `Delete`... and then click `Create access key` to generate a new set of keys. Make sure to download .csv file!).

(2) Head over to the terminal. 

Remember step 14 in my "Provision an EC2 Instance Using EC2's Amazon Linux 2 AMI" section?

 Or, in the Amazon console, go to EC2 > Running Instances. Click the box next to the instance name, and click on Description tab. Copy to clipboard the "IPv4 Public IP." Also make note of the "Key pair name" in this section.

 Navigate to the folder you downloaded (or moved) the **.pem** file into and type into the terminal:

```
ssh ec2-user@<IPv4 Public IP> -i <your Key pair name>.pem
```

--> Add screenshot of terminal here <-- 

Elevate your priviledges to root with:
```
sudo su
```
So, your terminal will have something like:
```
[root@ip-111-11-11-11 ec2-user]#
```

Now you can interact with services like S3.





### Resources
* A Cloud Guru's [AWS Certified Cloud Practitioner](https://acloud.guru/learn/aws-certified-cloud-practitioner) Course

# Elastic Beanstalk
Here are my step-by-step instructions on [how to deploy to elastic beanstalk](https://github.com/SharinaS/java-fundamentals/wiki/Deploying-to-AWS-with-Elastic-Beanstalk) when dealing with a Java fullstack app (and postgresql db)

# IAM
My Java 401 class notes on [IAM in my wiki](https://github.com/SharinaS/java-fundamentals/wiki/IAM---AWS-Identify-and-Access-Management) from my time at Code Fellows in Seattle. 

### About
* IAM = Identify And Access Management
* Security controls regarding access to AWS resources.

### Resources
* [1Strategy March blog post on IAM](https://www.1strategy.com/blog/2020/03/10/ten-tips-for-improved-iam-security/)