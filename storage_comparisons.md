# Storage Comparisons

||S3|EBS|EFS|
|-----|-----|------|-----|
|**Speed**|Slowest|Faster than S3|Faster than S3|
|**Latency**|Higher latency| lowest-latency access to data|Lower latency than S3 with high IOPS but worse than EBS|
|**Price**|Cheapest|cheaper than EFS| almost 10 times more expensive than Amazon EBS|
|**Accessibiliy**|Accessible from anywhere|Only available in a particular region|Can share files between regions on multiple EFS instances|
|**Data Access**|Accessible over internet based on access policy configured|Can only be accessed by a single Amazon EC2 instance|Can be accessed by 1 to 1000s of EC2 instances from multiple AZs, concurrently|
|**Ideal usage**|when the number of objects needs no limit|database backups and other low-latency interactive applications that require consistent, predictable performance.|large quantities of data, such as large analytic workloads that are too big to be stored on EBS.|
|**Scalability**|Highly scalable and managed service|Manual scaling|Highly scalable and managed service|
|**Durability**|Can withstand up to two concurrent AZ failures|Cannot withstand AZ failure without point-in time EBS Snapshots|Every file system object is redundantly stored across multiple Availability Zones so it can survive one AZ failure.|

# Resources

[Blog post on comparisons on Cloud.netapp](https://cloud.netapp.com/blog/ebs-efs-amazons3-best-cloud-storage-system)

[Article on DZone Cloud Zone on comparisons](https://dzone.com/articles/confused-by-aws-storage-options-s3-ebs-amp-efs-explained#:~:text=Amazon%20S3%20can%20be%20accessed,with%20a%20single%20API%20call.)
