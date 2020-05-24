# About
* CloudFormation automates the task of creating groups that power an application.
  * CF is a way to provide scripting for the cloud environment.
* Manages the lifecycle of the infrastucture
  * *Creates* AWS infrascture, based on a template
  * *Updates* the AWS infrascture as needed
  * *Deletes* the AWS infrascture reliably
* Uses a JSON or YAML-format file that describes all the AWS resources you need to deploy in order to run the application and the stack. 
  * Template is the YAML file, which is then uploaded via S3 to AWS, which kicks off CloudFormation.
* Includes a stack, which is the set of AWS resources that are created and managed as a single unit.
* 

**Quick start** is something on AWS that includes templates already built by AWS Solutions Architects, which allows you to create complex environments very quickly. 

## The YAML or JSON file includes:
(the following is taken directly from the AWS CloudFormation FAQs)
1. An optional list of template parameters (input values supplied at stack creation time)
2. An optional list of output values (e.g. the complete URL to a web application)
3. An optional list of data tables used to lookup static configuration values (e.g., AMI names)
4. The list of AWS resources and their configuration values
5. A template file format version number

# Simple CloudFormation Example
*Provision a  EC2 t2.micro instance with an AMI of Amazon Linux*

### Generals about the Template
A yaml file contains information about the instance, such as that it's a t2.micro, and what the AMI ID is. This is the template file.

```
Resources:
  Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0ce21b51cb31a48b8 # <----- Amazon Linux 2 AMI, accessed via the Amazon Console
```

A template has parameter-like bits of info
  * Resources are like the code block of your function. 
  * Type is like a datatype.

A Template can also have outputs - what values do I want to have available to other templates within my region?
```
Outputs:
  MyInstanceId:
```

Templates can have multiple resources
* bucket
* instance
* role
* etc

InstanceType
* Can set it to update (or similar command)
* The stack will keep up with what's in the template that's new. But, heads-up, when you do that in the console, CloudFormation will freak out (per Stephanie).

Template is like a constructor function. It can make many stacks. Every time I run the template, it will produce a stack
* Note - Stack is a bad name, because a CloudFormation stack is not a stack. A stack is the results of whatever happened in the Template.
* Template is the blueprint that describes the infrastructure.

### Stack Overview:
A stack will know:
* What template was used and parameter values
* Some metadata (name, creation date, etc)
* What resources were created by the stack
* The events that happened while those resources were created
* Outputs

A stack is a provisioned instanced of the template. 

Use the Console to Create the Stack. In CloudFormation, click on the button that says "Create Stack." Upload the template file you've made. 


 ### Change Set
 A Change set allows you to see how updating the stack will impact the resource.
 * Important, since sometimes you might accidentally delete something with a change. 

 You can create a change set before doing something. 

 A change set is thus a summary of proposed changes. If it's accepted, it will create an sns topic within the stack.  

# Basic Template Terminology - Resources
```
AWSTemplateFormationVersion:
Description:
Metadata:
Parameters:
Mappings:
Conditions:
Transform:
Resources:
Outputs:
```


Resources - The only required part of the template.

### EC2 Instance
```
Resources:
  MyEc2Instance: <--- choose a logical id
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: 'ami-0ce21b51cb31a48b8'
     
```

### S3 Bucket
AccessControl can be only one of the following:
* Private
  * Produces a bucket that has Access of "Objects can be public"
* Public Read
  * Allows for full public access
* PublicReadWrite
* AuthenticatedRead
* LogDeliveryWrite
* BucketOwnerRead
* BucketOwnerFullControl
* AwsExecRead

```
Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: sharinabucket <--- bucket name can't contain uppercase characters
      AccessControl: PublicRead
      WebsiteConfiguration:
        IndexDocument: index.html
```

### Security Group
```
Resources:
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
        FromPort: '22'
        ToPort: '22'
        Cidrlp: 0.0.0.0/0
```

# Intrinsic Functions
Built in functions, of which there are over 10 in CloudFormation. 

## Join
Appends a set of values into a single value

Syntax:
```
!Join[delimiter,[comma-delimited list of values]]
```
So, to have colon as the first item, and a, b, c as the second item would product:
```
!Join [ ":", [ a, b, c] ] 
```
= "a:b:c"

### Example

Basic example from above:
```
Resources:
  Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0ce21b51cb31a48b8 
      - Tags: "Name"
        Value: "Simple Example"
```

Now, add a join function:
```
Resources:
  Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0ce21b51cb31a48b8 
      - Tags: "Name"
        Value: !Join [ " ", [EC2, Instance, with, fn, Join ] ]
```

# Multiple Resources
The order of creation is very important. CloudFormation handles this for us. 

-------

# Hints
When you see !Ref, go look up variable.

# CLI Commands - CheatSheet
### Access information about a stack in CloudFormation:
```
aws cloudformation describe-stacks --stack-name sharina-s3bucket
```

# Resources
[AWS CloudFormation FAQs](https://aws.amazon.com/cloudformation/faqs/)
