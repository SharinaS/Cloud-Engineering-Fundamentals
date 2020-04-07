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