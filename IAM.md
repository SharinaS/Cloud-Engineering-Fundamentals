# IAM

IAM = Identity Access Management

IAM includes:

* Users
* Groups
* Roles
* Policies

IAM is universal

* No region needs to be chosen.

## Root Account

The account created when you first set up your AWS account.

It has complete Admin access.

## New Users

New users have *no permissions* when they are first created. 

* Least priviledged.

New users are assigned keys:

* Access Key ID
* Secret Access Keys
* Used to access AWS via the APIs and CLI
  * You can only view these keys once - store them in a secure spot!

## Login

### Multifactor Authentication

Always set this up on your root account.

### Password Rotation

You can set up rotation policies.

# Roles
## About
Roles are a way for one AWS service to use another AWS service.

Used to secure the AWS environment. Always use roles, since there is no access into the AWS environment, rather than saving credentials on an EC2 instance.

Roles are also easier to manage - can update policies easily, such as being able to provision other EC2 instances. Can apply roles to EC2 instances at any time, immediately. 

Roles are universal - don't need to specify what region they're in. 

## Roles and AWS CLI
Roles are more secure than storing your access key and secret access key on individual EC2 instances (ie, SSH into an instance, and then do `aws configure`)

Roles can be assigned to an EC2 instance after it's created, using both the console and the CLI. 

# Simple Demo
## Create a Role
In the Amazon Console, go to IAM > Access Management > Roles.

Hit the button `Create role`. 

### Choose the Service
Choose the type of AWS service that will use this role. We'll choose `EC2` to continue on with [demo from my EC2.md file](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md), and hit `Next: Permissions`.

### Attach Policy
We attach a policy to the new role. For this step through, we want S3 Admin Access, so scroll down the Policy list to choose `AmazonS3FullAccess`. This will give the ec2 instance we set up [in my EC2 notes](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md) full access to S3. 

![screenshot of policy summary](/assets/s3policysummary.png)

Click `Next: Tags`.
Tag if you like, but in this example for the S3 bucket, I'm going to skip this. 

Click `Next: Review`

### Give the Role a Name
Give a descriptive name (like, "S3AdminAccessForDemo"), and a description (like, "Allows EC2 to use S3 as an admin").

Click `Create role`.

![screenshot of amazon console after creating role](/assets/createRole.png)

### Attach the Role to the EC2 Instance
In the Amazon console, go to Services > EC2. 

Under Resources on the dashbourd, click on `Running instances` and select the checkbox next to the instance you set up. 

Click on `Actions` > `Instance Settings` > `Attach/Replace IAM Role`.

A page will show up that shows the instance ID and the name you gave it. The dropdown menu under it, called "IAM role," lets you choose the role you created above.

Click on `Apply` and then `Close`.

Head back over [to my EC2 notes](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/EC2.md), if you like. 


# Switching Roles

A company will have an organization within AWS. Within that organization, a role should have been created to allow users to assume the role in order to create sandboxes for learning. This is called configuring a role, which includes giving the account and role details. We'll call this role, "An-Admin."

* Perhaps the account in full is called "assumed/An-Admin/user@helloworld.com," and it has a trusted entity as the account number for the master account. 

When building the first part of a trust relationship - essential when preparing to switch roles - the administrator will create a new policy within the master account. It serves to say that the user, signed in within the master account, can switch to the roles listed within the policy.

***An Example Policy Found In the Master Account***
The 12-digit account number found under "Resource" refers to the account called An-Admin (not the master account).
```
{
  "Version": "2011-10-18",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action: "sts:RoleAssume",
      "Resource": [
        "arn:aws:iam:123456789012:role/An-Admin",
        "arn:aws:iam:123456789012:role/SharinaRole1",
        "arn:aws:iam:123456789012:role/SharinaRole2",
      ],
      "Condition": {
        "BoolIfExists": {
          "aws:MultiFactorAuthPresent": "true"
        }
      }
    }
  ]
}
```

The above policy, found in the master account, says that it's okay to trust the An-Admin role, the SharinaRole1, or the SharinaRole2. This policy, above, might be called "PolicyToAssumeSandboxRole."

The signed in user can now click on the Role name "An-Admin," and click on the link that has the word within the URL "switchrole." The URL should exists just above the tags called `Permissions`, `Trust relationships`, etc. You may notice that the policies attached to the "An-Admin" role do not include the policy we saw above, called "PolicyToAssumeSandboxRole."

When you click on the URL, you'll see a new screen called "Switch Role." It should have the Account and Role auto-filled, in this case with the Account referring to something like the master-acct, and the Role being the "An-Admin" role discussed above. You can choose a Display Name, which in this case, I named "Sandbox."

You should see in the upper right, where before your signin info is listed, now it shows "Sandbox." 

Now, let's create a policy that lets us switch from Sandbox to a role that we can create while in the Sandbox role. Note, we won't be able to create a role within the master level of the account, since that is something only the AWS administrator will have permissions for.

In Policies, hit `Create Policy`. This new policy is the second part of the trust relationship; the first part was written above by the AWS administrator in the master level of the account. Note that you must write the name of the role you want to create with the same syntax as what was written in the first half of the trust relationship! 

I'm calling this policy, "assume-role-sharina."
```
{
  "Version": "2011-10-18",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::123456789012:role/SharinaRole1"
    }
  ]
}
```

A couple things to note in the JSON just above. First, the 12-digit account number should be the same as what is in the first JSON example. This number refers to the account number of the Sandbox role, or the An-Admin role. Second, the value assigned to the key of "Action," is necessary - it describes the action that the policy will allow; in this case, to assume a new role :) 

You should now be able to go to Roles, and hit the button, `Create role.` Click the type of trusted entity as `Another AWS account`. Here, you'll put in as a Trusted entity the master level account number (not the An-Admin account number). 

The next page allows you to attach a permission policy (or more). You can filter the policies by "Customer managed," which lets me find the policy I just made called "assume-role-sharina."

Add some tags in the next page, which will help when you want to clean up your account.

The next page lets you name your role. **Name your role ***exactly*** as it is named in the first half of the trust policy, written by the AWS administrator, way back when!!** Check that the trusted entity is that master account number, and that the policy is what you just created. Hit `Create role`.

You should now see your role within Roles.

Now, to switch into the new role. Click on the link for the role, so in my case, I'd click on the role name of SharinaRole1. Click on the URL that is within the Summary of the role, just above the Permissions and Trust relationships tabs. Give your role a cool Display Name, and choose a color. Click `Switch Role`. 

You should now see the display name of the role you just created and assumed in the upper right hand of your AWS Management Console. 


# Resources
* A Cloud Guru's [AWS Certified Cloud Practitioner](https://acloud.guru/learn/aws-certified-cloud-practitioner) Course