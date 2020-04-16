# About EC2 Placement Groups

There are now 3 different types of placement groups.

A placement group is a way of placing EC2 instances.

Note that the name you use for a placement group must be unique within your AWS account. 

Instances that can be launched into a placement group:
* Compute Optimized
* GPU
* Memory Optimized
* Storage Optimized

You can't merge placement groups

You can move an existing instance *into* a placement group, as long as the instance is in the stopped state. 
* Moveable only via the AWS CLI or an AWS SDK 

## Clustered Placement Groups
A grouping of instances within a single AZ. 
* The only one that can't span multiple AZs

For apps that need low network latency, high network throughput, or both. 

Essentially a grouping of the instances very very close to each other. 

Recommended: homogenous instances within clustered placement groups.

## Spread Placememnt Group
Opposite from clustered. 

A spread placement group is a group of instances that are placed on distinct underlying hardware. 

For apps with a small number of crtical instances that should be kept separate from each other. 

You can have spread splacement groups inside different AZs, within one region.

Think of individual instances. Each one is completely isolated from another instance. If one rack fails, with an instance per rack, the other instances won't be affected. Designed to protect instances from hardware failure. 

## Partitioned Placement Group
Very similar to spread placement groups, but you can have multiple EC2 instances within a partition. 

Each partition within a placement group is on its on rack (own power). 