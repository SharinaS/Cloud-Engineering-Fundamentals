# Boot Strap and Metadata
Boot strap scripts are a way of automating your EC2 deployments.

This is all about writing "user-data"

Meta-data used to get information about an instance.

# Do some Bootstrap - Make User-Data
In the console, provision a new EC2 instance - Amazon linux AMI, with a t2.micro.

In the "Configure Instance Details," under "IAM role," give the instance "AdminAccess."
* Need IAM here, because in this example, we're going to create an S3 bucket using bootstrap script.

Scroll down on this same page, and go into "Advanced Details." the little box is where we can paste in or type boot strap script. 

Boot strap script always begins with a `#!`

Write the path to our interpreter:
* The interpreter takes commands and runs them at root level when we first provision our EC2 instance.
```
#!/bin/bash
```

When it boots up, do an update.
```
yum update -y
```

Then, install Apache and start it.
```
yum install httpd -y
service httpd start
```

Make sure that if the apache service stops, we want to make sure the Apache service comes back up on a reboot. 
```
chkconfig httpd on
```

Go to the html directory
```
cd /var/www/html
```

Create a little webpage and write it into an html file
```
echo "<html><h1>Hello World</h1></html>" > index.html
```

Then, make an S3 bucket
```
aws s3 mb s3://sharina-bootstrap-bucket
```

You can then back up the little web page to the S3 bucket.
```
aws s3 cp index.html s3://sharina-bootstrap-bucket
```

**So, the final boot strap text will be**:
```
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>Hello World</h1></html>" > index.html
aws s3 mb s3://sharina-bootstrap-bucket
aws s3 cp index.html s3://sharina-bootstrap-bucket
```

Click "Review and Launch." For this, you can leave everything as default, and just use a basic existing security group.
*  ... use a self-created security group (which has HTTP (source: 0.0.0.0/0), HTTP (::/0) and SSH (0.0.0.0/0) for inbound rules).

Launch it. 

You should now be able to just paste the public IP address into the URL and see the basic little webpage, and see an S3 bucket in the list of buckets with the index.html file in it. 

# Instance Metadata
Can go into the EC2 instance and look at the user data and instance metadata.

SSH into your EC2 instance and elevate privileges to root. 

Run a curl command to get a printout of the bootstrap script that was added (and run) in the AWS' console upon making an instance "Advanced Details" --> "User data" (can probably edit the user-data too, after an instance is created).
```
curl http://169.254.169.254/latest/user-data/
```

Get (many) options to choose from regarding getting metadata about the instance.
```
curl http://169.254.169.254/latest/meta-data/
```

For example, access info about the local IP address:
```
curl http://169.254.169.254/latest/meta-data/local-ipv4
```
The local IP address is then shown. 

... or show the public IP address:
```
curl http://169.254.169.254/latest/metadata/public-ipv4
```


We can output the data into a txt file:
```
curl http://169.254.169.254/latest/meta-data/public-ipv4 > public_ip.txt
```

When you do `ls`, you'll see the txt file.

Read the file with:
```
cat public_ip.txt
```

