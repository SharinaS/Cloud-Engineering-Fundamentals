# Shared Responsibility Model

---> For the Cloud Practitioner Exam <---

[Read the doc on AWS](https://aws.amazon.com/compliance/shared-responsibility-model/)

AWS manages security **OF** the cloud, but security **IN** the cloud is the customer's responsibility. 

Customer retains control of what security they choose to implement.

> Customers should be aware that their responsibilities may vary depending on the AWS services chosen.  For example, when using Amazon EC2, you are responsible for applying operating system and application security patches regularly. However, such patches are applied automatically when using Amazon RDS.

|AWS| Customer|
|-----|-----|
|*responsible for security of the cloud* | *responsible for security in the cloud* |
|regions | Client-side data |
|AZs | Server-side data |
|Edge locations | Networking traffic protection |
|Compute | OS (EC2), network and firewall configuration |
|Storeage | Platform, applications, identity and access management |
|Networking | Customer data
|Database
|Software
|Hardware and infrastructure |
|||

## Security Responsiblities

| AWS | Customer |
|-----|-----|
|Facilities| AMIs|
| Physical Security of hardware | Operating systems|
| Network infrastructure| Applications|
|Virtualization infrastructure| Data in transit|
||Data at rest|
||Data stores|
||Credentials|
||Policies|
||Configurations|

## Notes on Services

You do patches on EC2. 

AWS is responsible for the OS on which S3 or RDS runs on.

Installing the database software is AWS’ responsibility.

## Shared Controls

Shared Controls are controls which apply to both the infrastructure layer and customer layers, but in completely separate contexts or perspectives. In a shared control, AWS provides the requirements for the infrastructure and the customer must provide their own control implementation within their use of AWS services. --*AWS Certified Cloud Practitioner Practice Exam Course*

### Examples include

* Patch Management – AWS is responsible for patching the underlying hosts and fixing flaws within the infrastructure, but customers are responsible for patching their guest OS and applications.

* Configuration Management – AWS maintains the configuration of its infrastructure devices, but a customer is responsible for configuring their own guest operating systems, databases, and applications.

* Awareness & Training - AWS trains AWS employees, but a customer must train their own employees.

## Inherited Controls

Inherited Controls are controls which a customer fully inherits from AWS such as **physical controls and environmental controls**.

As a customer deploying an application on AWS infrastructure, you inherit security controls pertaining to the AWS physical, environmental and media protection, and no longer need to provide a detailed description of how you comply with these control families.

For example: Let’s say you have built an application in AWS for customers to securely store their data. But your customers are concerned about the security of the data and ensuring compliance requirements are met. To address this, you assure your customer that “our company does not host customer data in its corporate or remote offices, but rather in AWS data centers that have been certified to meet industry security standards.” That includes physical and environmental controls to secure the data, which is the responsibility of Amazon. Companies do not have physical access to the AWS data centers, and as such, they fully inherit the physical and environmental security controls from AWS.
--*AWS Certified Cloud Practitioner Practice Exam Course*
