# About EFS
EFS = Elastic File System

EFS is a file storage service for Elastic Compute Cloud or EC2 intances. It provides a (simple) interface for creating and configuring file systems.
* Has Read after Write Consistency

EFS makes storage capacity elastic, as in, it grows and shrinks automatically as you add/remove files.

In contrast, EBS allows only for mounting a virtual disk to one EC2 instance. You can have an EC2 instance or more sharing an EBS volume. You can have them sharing an EFS volume. 

If you provision an EFS instance, it will just grow automatically. You add a terabite file, and it grows. 
* Can support thousands of concurrent NFS connections
* Can scale up to the petabytes

EFS supports NSFv4 (Network File System version 4) protocol

### Regions:
Data is stored across multiple availability zones within a region

### Billing:
You only pay for the storage you use (no pre-provisioning required).

# Store a website on EFS
### Go to EFS

Click "Create file system"

In "Configure file system access," you choose the VPC it will go into. Certainly, for a default VPC, in "Create mount targets," the security groups will all be default. 

Click Next to get to the next page.

Add tags. 

Decide if you want lifecycle management (will automatically move your files over to a cheaper storage if the files aren't accessed frequently / at all). None is fine for a demo.

"Bursting" is fine for "throughput mode"

"General purpose" is fine for "performance mode"

Check the box to "Enable encryption of data at rest" and use the KMS master key

Click Next, and then "Create file system."

### Provision EC2 instances
Make a couple Amazon Linux 2 instances, t2.micro. In "configure Instance Details," at the top of the page, change the number of instances to "2."

For this example, leave it all as default, but create a bootstrap script down below:
```
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
yum install -y amazon-efs-utils
```
The last line says to install the amazon efs utils tool in order for us to mount our file system to our ec2 instances later. 

Click next, and leave everything as default for the next few pages, except choose to use your own securty group (which has which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules)

### Check out the Security Groups
We provisioned our EFS into a default security group. We need to make sure to modify our inbound rules, since we're going to create a webserver.

Go to "Inbound" rules tabl and click "Edit."

The first tab will say "All traffic" as the Type. 

The second rule should be "NFS," and the Source should be a custom security group that you made at another moment (which has which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules). This means its letting in NFS protocol into our default security group from our webservers. 

### SSH into the instances
Copy the public IP address of one of the instances. 

SSH into this first instance and elevate your privileges to root. 

Make a new tab your terminal and SSH into the second instance and elevate privileges to root.  

To make sure apache is installed, simply check to see if this directory is installed: 
```
cd /var/www/html 
```
You can do this for both instances.

Make sure to go back a directory with `cd ..`, so your terminal looks like this:
```
[root@ip-111-11-11-11 www]#
```

### Mount the EFS to our EC2 instances:
Go to EFS on the console.

Click the radio button for the file system you created. Scroll down to the link under DNS name that says: "Amazon EC2 mount instructions (from local VPC)"

A window will pop up that has mount instructions. Scroll down to part 3. Because we have encryption at rest turned on (thus we want to encrypt data in trasit), we want to use the tls command. Note that you won't need sudo, because we're already at the root. 

Click on one of the terminal tabs and paste in the following to the terminal, and append on the directory we want (html directory):
```
mount -t efs -o tls fa-9816b269:/ /var/www/html
```

Do the same thing for the other tab. 

If it hangs for more than a minute, your security groups aren't working.

check your www directory with 'ls`. There should be cgi-bin and html. There won't be anything inside the html folder. 

Can create a little demo file while cd'd into html and output it to a new index.html file.
```
echo "<html><body><h1>hello world</h1></body></html>" > index.html
```

You can check the contents of the file with:
```
cat index.html
```

Now, head over to the other terminal tab, where the other instance is that you SSH'd into. `cd` into the html directory, and check that there's an index.html file in there. a `cat index.html` will show the same contents that were in the other instance! You can change the file from one instance, and the other instance will see those same changes, since they are both on the same file system :) 