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
|Hardware and infrastructure

You do patches on EC2. AWS is responsible for the OS on which S3 or RDS runs on.

## Shared Controls

Shared Controls are controls which apply to both the infrastructure layer and customer layers, but in completely separate contexts or perspectives. In a shared control, AWS provides the requirements for the infrastructure and the customer must provide their own control implementation within their use of AWS services. --*AWS Certified Cloud Practitioner Practice Exam Course*

### Examples include

* Patch Management – AWS is responsible for patching the underlying hosts and fixing flaws within the infrastructure, but customers are responsible for patching their guest OS and applications.

* Configuration Management – AWS maintains the configuration of its infrastructure devices, but a customer is responsible for configuring their own guest operating systems, databases, and applications.

* Awareness & Training - AWS trains AWS employees, but a customer must train their own employees.
