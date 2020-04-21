Everything fails... at some point. So, always plan for failure. 

### Some highly available architecture
User --> Route 53 (with health check --> Regions

Region 1 has:
* An auto scaling group
* public subnets
  * with webserver in each
  * with Nat gateway in each 
* private subnets 
  * with DB servers

Region 2 has:
* An auto scaling group
* public subnets
  * with webserver in each
  * with Nat gateway in each 
* private subnets 
  * with DB servers

Because of all this replication, we have architecture that allows for a region failure or an AZ failure. That way, if one goes down, the other region can pick up the slack, or the other AZ can pick up the slack, depending on the scenario. 

### Example Scenario
Requirement: Website requires a minimum of 6 instances. It must be able to tolerate the failure of 1 AZ. The ideal architecture (while being cost effective) is...

... 3 AZs with 3 instances in each AZ. 
* 9 instances in total is needed, b/c if we lose 1 AZ we'll still have 6 instances to look after the environment. 

### Multi-AZ vs Read Replicas for RDS
Multi-AZ: For disaster recovery
Read Replicas for RDS: For performance

### Scaling out vs Scaling up
Scaling out: Where we use auto scaling groups and we add additional EC2 instances. 

Scaling up: Where we increase the resources inside our EC2 instances
* Go from a t2.micro to a 6x extra large.

### S3 storage classes
Standard S3 and Standard S3 Infrequently Accessed are highly available.

*Not* highly available: Reduced redundancy storage, S3 single AZ

# Make a HA Word Press Site
Tech:
* Route53
* Elastic Load Balancer
* EC2 instances
* Auto Scaling Group
* Webserver security group
* RDS security group
* RDS instances - MySQL database
* Multi-AZs
* S3 buckets
* CloudFront

Refer to A Cloud Guru, "AWS Certified Solutions Architect" course for step-by-step instructions on building this - chapter 8.7