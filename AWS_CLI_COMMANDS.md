# Contents
* Setting up the CLI Command Line - super basics for demoing
* SSH into an instance
* Install MySQL
* Apache
* Use Describe for various services
* Checking AWS CLI credentials from the terminal - configuration, etc.
* Resources list

# Setting up the CLI Command Line
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
Don't do this in a production environment. 

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
```
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


# Install MySQL
yum install mysql -y

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

--------------
# Resources
[AWS CLI Command Reference guide](https://docs.aws.amazon.com/cli/latest/reference/)