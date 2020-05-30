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