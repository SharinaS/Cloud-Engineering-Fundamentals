# Compliance

AWS has checked off many compliances. 

You can get copies of the reports and give them to your auditors.

On the console, go to AWS > Security, IDentify, & Compliance > Artifact

* Has a whole list of access-controlled docs

Examples: 

* A PCI DSS Level 1 certification attests to the security of the AWS platform regarding credit card transactions.
* A HIPAA certification attests to the fact that the AWS Platform has met the standard required for the secure storage of medical records in the US

# Shared Responsibility Model

[Read the doc](https://aws.amazon.com/compliance/shared-responsibility-model/)

AWS manages security **OF** the cloud, but security **IN** the cloud is the customer's responsibility. 

Customer retains control of what security they choose to implement.

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
|Hardware and infrastructure

You do patches on EC2. AWS is responsible for the OS on which S3 or RDS runs on.

# WAF

See file on this

Protects you against hackers

# AWS Shield

managed Distributed Denial of Service (DDos)

Protects you against DDos attacks

* Attacker sends so much traffic to the app that the app stops working.

Shield is turned on automatically with all new AWS accounts.

There is:

* Standard
  * Comes automatically with all AWS accounts
* Advanced
  * Offers automated application layer monitoring (layer 7) - traffic monitoring.
  * AWS will reimburse will reimburse costs related to:
    * Route 53
    * CloudFront 
    * ELB DDoS
  * $ 3000/mo

# Inspector

Automated security assessment service for your EC2 instance(s).

* Looks for common vulnerabilities in the EC2 instance
  * "These patches need to be applied"

Helps improve sercurity and compliance of applications deployed on AWS. 

You install it into your instance

A report is produced after evaluation.

# Trusted Advisor

Helps you provision your resources following best practices on AWS

* optimize your entire AWS environment in real time

Advises on (helps you assess):

* Cost Optimization
* performance
* security
* fault tolerance.

> Looks at entire AWS account, not just EC2.

Includes:

* Core Checks
  * Free
* Full Trusted Advisor
  * Business and Enterprise companies only

# CloudTrail

In Management and Governance

See file on this

Records console actions and api calls to increase visibility into your user and resource activity. 

# CloudWatch

See file on this

Lets you monitor *performance*. 

# Config

Monitors the *configuration* / settings of your AWS environment.

* how the resources are related to one another 
* how they were configured in the past versus how they change

Ie, security groups changed = config question.

# Athena

See file on this

# Macie 

See file on this


