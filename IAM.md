# Roles
Used to secure the AWS environment. Always use roles, since there is no access into the AWS environment, rather than saving credentials on an EC2 instance.

Roles are also easier to manage - can update policies easily, such as being able to provision other EC2 instances. Can apply roles to EC2 instances at any time, immediately. Roles are universal - don't need to specify what region they're in. 

## Create a Role
In the Amazon Console, go to IAM > Access Management > Roles.

Hit the button `Create role`. 

### Choose the Service
Choose the type of AWS service that will use this role. We'll choose `EC2` to continue on with [demo from my EC2.md file](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md), and hit `Next: Permissions`.

### Attach Policy
We attach a policy to the new role. For this step through, we want S3 Admin Access, so scroll down the Policy list to choose `AmazonS3FullAccess`. This will give the ec2 instance we set up in [my EC2 notes](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md) full access to S3. 

<!-- screenshot here -->

Click `Next: Tags`.
Tag if you like, but in this example for the S3 bucket, I'm going to skip this. 

Click `Next: Review`

### Give the Role a Name
Give a descriptive name (like, "S3AdminAccessForDemo"), and a description (like, "Allows EC2 to use S3 as an admin").

Click `Create role`.

<!-- screenshot here -->

### Attach the Role to the EC2 Instance
In the Amazon console, go to Services > EC2. 

Under Resources on the dashbourd, click on `Running instances` and select the checkbox next to the instance you set up. 

Click on `Actions` > `Instance Settings` > `Attach/Replace IAM Role`.

A page will show up that shows the instance ID and the name you gave it. The dropdown menu under it, called "IAM role," lets you choose the role you created above.

Click on `Apply` and then `Close`.

Head back over to the EC2 notes, if you like. 
