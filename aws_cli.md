# Commands Often Used to Work With the AWS CLI

## Index

* [Accounts I'm in within AWS](#Account-You-Are-In---How-To-Tell)
* [AMI Properties](#AMI-Properties)
* Apache
* [Dig command](#Using-Dig-to-Query-DNS) - DNS query
* [Setting up the CLI Command Line](#Setting-up-the-AWS-CLI) - super basics for demoing
* SSH into an instance
  * [SSH Into a Private Instance](#SSH-Into-a-Private-Instance)
  * [SSH into a Public EC2 Instance](#SSH-into-a-Public-EC2-Instance)
* Install Databases (RDS)
  * [Install MySQL Using Yum](#Install-MySQL)
  * [Install PostgreSQL Using Yum](#Install-PostgreSQL)
* [Use Describe](#Describe---Access-Information-about-a-Service) for various services
* Checking AWS CLI credentials from the terminal - configuration, etc.
* Resources list

-----
`less` command

`ls /etc/resolve.conf`
`cat /etc/resolve.conf`

aws nameservers will be on the .2 of the subnet --> ie, 10.7.0.2. On amazon, they are always local. 

barehost name given, then you'll get search us-west-2

# Account You Are In - How To Tell
```
aws sts get-caller-identity
```

```
aws iam list-account-aliases
```

# Query DNS
## Dig
Get info about the mail systems used:
```
dig -t mx 1strategy.com
```
 or `dig -t soa 1strategy.com` or `dig -t TXT 1strategy.com`

Use of `dig` to query a single host

With the domain name:
```
dig domain-name.com
```
With the public ip address of the webserver:
```
dig 11.111.11.111
```
To get just the IP address:
```
dig domain-name.com +short
```
Follow the route / tree taken to find the IP address:
```
dig domain-name.com +trace
```

```
dig myserver
```

## Host
```
host 1strategy.com
```

```
host www.1strategy.com
```

## TraceSearch 
Sometimes not as helpful as just Dig.

## WhoIs
```
whois 1strategy.com
```

### References: 
* [Understanding the Dig Command](https://mediatemple.net/community/products/dv/204644130/understanding-the-dig-command)
* [10 Dig Commands](https://www.tecmint.com/10-linux-dig-domain-information-groper-commands-to-query-dns/)

# Setting up the AWS CLI
Basic setup: Set up a user in IAM.
* Needs to have "Programmatic access," re: Access type. 
* When setting up permissions, create a group that has "Administrator Access" applied to the group. 
* Click next a couple times to then be able to "Create user."
* Download that .csv file that gives the secret access key id.

### Don't store your access keys on individual EC2 instances
Setup roles instead, and store public and private keys inside .aws' config and credentials files. See notes below on checking AWS CLI credentials from the terminal.


# To Use the AWS CLI 
Type `aws`.

Type aws + service + what you want to do

For help on various things you can do:
```
aws help
```

To access the aws hidden directory, which contains at the least, config and credentials
```
cd .aws
```


--------------

# SSH into a Public EC2 Instance 

Via the terminal, navigate to where you stored your KeyPair. 

If it's a new key pair, you'll have to establish the appropriate permissions with a chmod command:

```
chmod 400 SharinaKeyPair.pem 
```

Grab the IPv4 Public IP value (the IP address) of the public instance you made. 

To SSH into the instance, type into the terminal the following, plus the IP address, plus the file name for the pem file. 

```
ssh -i my-key-pair-name.pem ec2-user@my-public-ip-address 
```
For ease, you can elevate priviledges to root:

```
sudo su
```
... but it's better not to. Use sudo infront of specific commands instead. 

# SSH Into a Private Instance
> Don't do this in a production environment. 

## Shortcut Using `-J`

SSH into the jumpbox, and then immediately into the webserver, using the same private key, all in one command:

```bash
ssh -J ec2-user@public-ip-address ec2-user@private-ip-address -i id_rsa.pem
```

## Long way Using `-i`

SSH into your public instance (see above).

In a separate terminal tab, navigate to where your keypair.pem file is, and open it up with an editor like VSCode. Copy the contents (info regarding your private key). 

Back in your terminal, where you're ssh'd into your public instance, open Nano and make a new file
```
nano new-key-pair-name.pem
```
Inside nano, paste the private key info. Save and exit. 

Check that the file is inside your ec2-user directory
```
ls-la
```

Go to your console and copy the private IP address of your private instance.

SSH into your private instance, using sudo:

```bash
 sudo ssh -i new-key-pair-name.pem ec2-user@my-private-ip-address
```

If you need to check to see if a file exists in the private instance, now you can ls into it:

```
ls -la
```

or 
```
ls-a
```

# Install Databases

## Install MySQL

```bash
yum install mysql -y
```

## Install PostgreSQL

Applies to Fedora, Red Hat, CentOS, Scientific Linux, such as for a webserver EC2 instance

Try (haven't done this yet) 

```
yum install postgresql-server

```

Or try following this resource:
http://postgresguide.com/setup/install.html

# Apache
Once you've accessed the EC2 instance (a public one)...

## Check for existance of Apache

```
service httpd status
```

## Install Apache
```
yum install httpd -y
```
then,

```
chkconfig httpd on
```
then,

```
service httpd start
```

## Go to html directory

```
cd /var/www/html
```

there may not be anything in there... `ls`

Use nano to make a little index page if you want, with `<html><h1>hello</h1></html>`. 

```
nano index.html
```

You can check the code in the terminal with

```
cat index.html
```

Once Apache is setup, you can copy the public IP address of an instance, and paste it into a web browser window, and hit enter to see your webpage :)



# Describe - Access Information about a Service
## Stack in CloudFormation:
```
aws cloudformation describe-stacks --stack-name sharina-s3bucket
```

## EC2
### Specific Instance
```
aws ec2 describe-instances --instance-ids i-12345678901234567
```

## Application Load Balancer
Get info about all your load balancers:
```
aws elbv2 describe-load-balancers
```

# S3
List buckets
```
aws s3 ls
```

Make a bucket
```
aws s3 mb s3://sharina-cli-bucket
```

# Setting up AWS CLI Credentials / Profile and Checking Them via the CLI
You can either set up credentials through the terminal with `aws configure`, or you use VS Code to store the credentials so you always have access to the CLI. 


Get into aws
```
cd .aws
```

Check what's inside - you should see at minimum, `cli config credentials`. 
```
cd .aws
```

Open config and credentials using vs code

```
code credentials config
```
Modify the files as needed
* Account #
* Alias
* Role
* mfa_serial


Check the default profile (or whatever profile is setup)

```
sharstubbs~/.aws$ aws sts get-caller-identity --profile default 
```

Check the training profile

```
sharstubbs~/.aws$ aws sts get-caller-identity --profile training 
```

You can also check the account aliases

```
sharstubbs~/.aws$ aws iam list-account-aliases 
```

... and then go deeper in, by looking at a particular profile via IAM

```
sharstubbs~/.aws$ aws iam list-account-aliases --profile training 
```

### NOTE: 

If you're using a different training account, and you have a default and perhaps a "profile training" account, make sure to add `--profile training` to the end of each command. 

# AMI Properties

Get a list of AMI name properties:

```bash
aws ec2 describe-images --owners amazon --filters "Name=name,Values=amzn*" --query 'sort_by(Images, &CreationDate)[].Name'
```

Get the latest Amazon Linux AMI ImageID value by region:

```bash
aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 --region us-east-2
```

Query for the Complete list of AWS Amazon Linux Parameter Store namespaces available:

```
aws ssm get-parameters-by-path --path "/aws/service/ami-amazon-linux-latest" --region us-east-2
```
--------------
# Resources
[AWS CLI Command Reference guide](https://docs.aws.amazon.com/cli/latest/reference/)