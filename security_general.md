# General Security Concepts in AWS

## Index (not complete)

* [Artifact](#Artifact)
* [Config](#Config)
* [GuardDuty](#GuardDuty)
* [Who To Contact - Abuse Team](#Abuse-Team)

-----------
-----------

# Artifact

AWS has checked off many compliances.

You can get copies of the reports and give them to your auditors.

On the console, go to AWS > Security, IDentify, & Compliance > Artifact

* Has a whole list of access-controlled docs
* Has on-demand access to security and compliance reports and select online agreements. 

Examples: 

* A PCI DSS Level 1 certification attests to the security of the AWS platform regarding credit card transactions.
* A HIPAA certification attests to the fact that the AWS Platform has met the standard required for the secure storage of medical records in the US

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

Automated **security assessment** service for your EC2 instance(s).

* Looks for common vulnerabilities in the EC2 instance
  * "These patches need to be applied"
* Looks for deviations from security best practices

Helps improve sercurity and compliance of applications deployed on AWS. 

You *install it into your instance*

A report is produced after evaluation.

# Trusted Advisor

Helps you provision your resources following best practices on AWS

* optimize your entire AWS environment in real time

**Advises on** (but doesn't ensure):

* Cost Optimization
* performance
* security
* fault tolerance.

Looks at entire AWS account (doesn't look within EC2 instances - use Insector for this)

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

Lets you monitor *performance* of a service.

# Config

See file on this

# Athena

See file on this

# Macie 

See file on this

# GuardDuty

Monitors the security of an account's AWS environment.

Analyzes:

* VPC Flow Logs
* CloudTrail logs 
* DNS query logs

Can integrate with Amazon CloudWatch Events, to create GuardDuty alerts

# Authorization vs Authentication

Authentication checks who is accessing the system, and passes that info to the authorization porcess.

The Authorization process checks what permissions the user has in AWS.

# Abuse Team

 The **AWS Abuse team** can assist you when AWS resources are being used to engage in the following types of abusive behavior:

I. Spam: You are receiving unwanted emails from an *AWS-owned IP address*, or *AWS resources are being used* to spam websites or forums.

II. Port scanning: Your logs show that one or more *AWS-owned IP addresses* are sending packets to multiple ports on your server, and you believe this is an attempt to discover unsecured ports.

III. Denial of service attacks (DOS): Your logs show that one or more *AWS-owned IP addresses* are being used to flood ports on your resources with packets, and you believe this is an attempt to overwhelm or crash your server or software running on your server.

IV. Intrusion attempts: Your logs show that one or more *AWS-owned IP addresses* are being used to attempt to log in to your resources.

V. Hosting objectionable or copyrighted content: You have evidence that *AWS resources are being used to host* or distribute illegal content or distribute copyrighted content without the consent of the copyright holder.

VI. Distributing malware: You have evidence that *AWS resources are being used to distribute* software that was knowingly created to compromise or cause harm to computers or machines on which it is installed.

-- From *AWS Certified Cloud Practitioner Practice Exam Course*

