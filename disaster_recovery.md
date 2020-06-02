# Disaster Recovery Architecture

* Backup restore
* Pilot light
* Warm standby
* Multi-site

||||||
|-----|-----|-----|-----|-----|
||Backup Restore (BR) |Pilot Light (PL)| Warm Standby (WS)|Multi-site (MS)|
|**Speed of system restoration**|slowest|> BR / < PL| > PL / < MS | fastest - there's no downtime and minimal data loss.|
|**Configuration type**||active/passive failover|active/passive failover| active-active|
|**Philosophy**||Configure and run the most critical core elements of your system. At recovery, provision a full-scale prod env't around the critical core.|A scaled down version is always running in the cloud.|A 1:1 copy of your infrastructure running in another region or AZ|
|**Method**|Frequent snapshots of EBS and/or RDS stored in S3|DB servers are configured for data mirroring replication. At recovery, use EBS snapshots and EC2 AMIs.|Minimum # servers with the smallest sizes possible are kept running. At failover, resize them and add scaling capabilities, and an ELB.|Route53 weighted routing, autoscaling, RDS multi-AZ feature|
|**Combinations**| Used in combo with other DR plans||||
|**Downside**||If data is constantly changing, you'd have to reverse replicate your data in the DR site.|If data is constantly changing, you'd have to reverse replicate your data in the DR site.|If data is constantly changing, you'd have to reverse replicate your data in the DR site.|
|**Cost**|||$$$|$$$$|

See more on [tutorialsdojo](https://tutorialsdojo.com/backup-and-restore-vs-pilot-light-vs-warm-standby-vs-multi-site/)

## Route53 Routing

Use Route53 to automatically route the live traffic to the disaster recovery (DR) environment only in the event that the primary application stack experiences an outage. You can use weighted routing to route production traffic to different sites that deliver the same application or service.

### Configurations

You can use Route 53 health checking to configure **active-active and active-passive** failover configurations. 

#### Active-Active

You configure active-active failover using **any routing policy (or combination of routing policies) *other than failover.***

Use this failover configuration when you want all of your resources to be available the majority of the time. When a resource becomes unavailable, Route 53 can detect that it's unhealthy and stop including it when responding to queries.

In active-active failover, all the records that have the same name, the same type (such as A or AAAA), and the same routing policy (such as weighted or latency) are active unless Route 53 considers them unhealthy. 

Route 53 can respond to a DNS query using any healthy record.

An Active-Active Failover uses all available resources all the time without a primary nor a secondary resource.So, you cannot set up an Active-Active Failover with One Primary and One Secondary Resource; you have to configure an active-active failover with something like a weighted routing policy. 

#### Active-Passive

You configure active-passive failover using the **failover routing policy**. 

Use an active-passive failover configuration when you want a primary resource or group of resources to be available the majority of the time and you want a secondary resource or group of resources to be on standby in case all the primary resources become unavailable. 

When responding to queries, Route 53 includes only the healthy primary resources. If all the primary resources are unhealthy, Route 53 begins to include only the healthy secondary resources in response to DNS queries.

[See my Route53 doc](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/route53_and_dns.md).