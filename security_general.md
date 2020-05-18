# Compliance

AWS has checked off many compliances. 

You can get copies of the reports and give them to your auditors.

On the console, go to AWS > Security, IDentify, & Compliance > Artifact

* Has a whole list of access-controlled docs

# Shared Responsibility Model

[Read the doc](https://aws.amazon.com/compliance/shared-responsibility-model/)

AWS manages security of the cloud, but security in the cloud is the customer's responsibility. 

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