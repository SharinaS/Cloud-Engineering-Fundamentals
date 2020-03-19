# Build a Simple Web Server
This uses the EC2 instance created above, and uses Apache.

Make sure you've got an EC2 instance set up. [See the instructions in my EC2 file](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md) in this repo.

### Apache
* Apache = Apache HTTP Server. 
* As a webserver, it serves websites on the internet.
* It's cross-platform web server software
* Open-source
* First version released in 1995 - one of the oldest web servers.

## Do the Things
### Get info from AWS
Go to the AWS console and get the IP address for the instance you want to use. The IP address is the "IPv4 Public IP" visible when the instance radio button is clicked. Make note of the "Key pair name" a little below the IP address.

### Connect from the terminal
Navigate to the folder you downloaded (or moved) your **.pem** file into (which has your private key for the EC2 instance) and type into the terminal:

```
ssh ec2-user@<IPv4 Public IP> -i <Key pair name>.pem
```
```
sudo su
```
Check for updates:
```
yum update -y
```

### Install Apache
To make the EC2 instance a web server, we install Apache via the terminal:
```
yum install httpd -y
```

### Start Apache Service
```
service httpd start
```

### Check the Website Root Directory
Go to the html directory:
```
cd /var/www.html
```
This is our root directory to our website. So, any files put into the directory will be visible just by going to the public IP address of the server. 

Check for content:
```
ls
```

### Make Some Content
At this point, just after adding Apache, there shouldn't be any files. 

Make the content by using `nano index.html` to open up a super simple text editor and make a super simple demo page (use control-x to exit, hit yes to save, then hit enter to write). 

Or, go about the usual steps, like using `touch` to make a file. You can't open up VS Code while in this directory though....

### View the Content on the Browser
Paste in the Public IP Address.

<!-- screenshot of hello world -->

# Build A Fancier One

# Resources
* A Cloud Guru's [AWS Certified Cloud Practitioner](https://acloud.guru/learn/aws-certified-cloud-practitioner) Course