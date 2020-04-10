## Access a Public EC2 Instance (the Web Server)

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

## Install MySQL
yum install mysql -y

## Apache
Once you've accessed the EC2 instance (a public one)...

### Check for existance of Apache

```
service httpd status
```

# Apache 
### Install Apache
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

Go to html directory

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

Once Apache is setup, you can copy the public IP address of an instance, and paste it into a web browswer window, and hit enter :)



# Access Information about a Service
## Access information about a stack in CloudFormation:
```
aws cloudformation describe-stacks --stack-name sharina-s3bucket
```

## EC2
### Specific Instance
```
aws ec2 describe-instances --instance-ids i-12345678901234567
```

# Setting up AWS CLI Credentials / Profile and Checking Them via the CLI
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


--------------
# Resources
[AWS CLI Command Reference guide](https://docs.aws.amazon.com/cli/latest/reference/)