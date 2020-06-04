# Storage Gateway

## Index

* [About](#About-Storage-Gateway)
* [Download](#Download)
* [Cached Volumes](#Cached-Volumes)
* [File Gateway](#File-Gateway)
* [Tape Gateway](#Tape-Gateway)
* [Stored Volumes](#Stored-Volumes)
* [Types of Storage Gateways](#Types-of-Storage-Gateways)
* [Volume Gateway](#Volume-Gateway)

# About Storage Gateway

AWS Storage Gateway connects an on-premises software appliance with cloud-based storage to provide seamless integration with data security features between your on-premises IT environment and the AWS storage infrastructure. You can use the service to store data in the AWS Cloud for scalable and cost-effective storage that helps maintain data security.

* The gateway connects to AWS storage services, such as Amazon S3, Amazon S3 Glacier, Amazon S3 Glacier Deep Archive, Amazon EBS, and AWS Backup
  * Backup is a fully managed backup service. Backup automates and consolidates backup tasks. It's a policy-based backup solution.

Goal: Provide seamless and secure integration between on-premises IT environment and AWS' storage infrastructure. 

> Outcome: You can store data in the cloud scalably and cost-effectively. 

There are virtual or physical storage gateways available.

## Storage gateway supports

* VMware ESXi
* Microsft Hyper-V (as the hypervisor)

## Download

You download the Storage Gateway's software appliance as a *virtual machine image*.

* Install it on a host in your data center
* Associate it with your AWS account via activation process
* Use the AWS management console to create the storage gateway option that is right for the business.

# Types of Storage Gateways

There are 3 types

* File Gateway
* Volume Gateway
* Tape Gateway

## File Gateway

Two types:

* NFS - Network File System
* (SMB)

> A way to store files directly into S3. For "flat files"

* A way to store your application *server data* in S3.

Files are stored as objects in S3 buckets. 

Files are accessed through a *Network File System (NFS)* mount point.

Application Server ----- *via NFS* ----> Storage Gateway ----> Direct Connect / internet / Amazon VPC ------> Amazon S3

S3 stores the following in the user-metadata of the object associated with the file:

* ownership
* permissions
* timestamps

After transfer to S3, objects are managed as native S3 objects.

* Bucket policies apply directly to objects stored in your bucket
  * versioning
  * lifecycle management
  * cross-region replication

## Volume Gateway 

Uses iSCSI - "eye-scuzzi"

> A way to store copies of your virtual harddisk drives in S3 (looks like EBS snapshots).

Two types:

* Stored volumes 
* Cached volumes 

The volume interface presents your applications with disk volumes using the iSCSI block protocol.

* You can use virtual harddisk drives. 
* Data written to the disk volumes can be asynchronously backed up.
* Backup data is point-in-time snapshots of your volumes.
  * They are stored as Amazon *EBS snapshots*.
  * Snapshots are incremental backups - they only capture *changed blocks*. 
  * All snapshot storage is compressed to minimize storage charges.

### Stored Volumes

> Entire dataset is stored on site and is asynchronously backed up to S3

Lets you store your primary data locally, so you can have a complete copy of your data locally in your datacenter.

> Provides low-latency access to the *entire dataset* (unlike cached volume gateway)

Create storage volumes and mount them as iSCSI devices from your on-premises application servers.

Data written to your stored volumes is stored on your on-premises hardware.

Stored volumes asynchronously backs up your data to AWS S3.

* Durable, off-site backups
* Backup is stored as Elastic Block Store (EBS) snapshots

### Cached Volumes

> Entire dataset is stored on S3, and only the most frequently accessed data is cached on site.

It doesn't deal with the entire dataset locally; it works with the most frequently used data.

By using cached volumes, you can use Amazon S3 as your primary data storage, while retaining frequently accessed data locally in your storage gateway. Cached volumes minimize the need to scale your on-premises storage infrastructure, while still providing your applications with low-latency access to frequently accessed data. You can create storage volumes up to 32 TiB in size and afterwards, attach these volumes as iSCSI devices to your on-premises application servers. When you write to these volumes, your gateway stores the data in Amazon S3. It retains the recently read data in your on-premises storage gateway's cache and uploads buffer storage.

Cached volumes can range from 1 GiB to 32 TiB in size and must be rounded to the nearest GiB. Each gateway configured for cached volumes can support up to 32 volumes for a total maximum storage volume of 1,024 TiB (1 PiB).

In the cached volumes solution, AWS Storage Gateway stores all your on-premises application data in a storage volume in Amazon S3. 

Size:

* Storage volumes up to 32 TiB (terrabytes) and attach them as iSCSI devices from on-premises application servers.

Cached volumes is a way of caching the most actively used data, versus the entire dataset regarding stored volumes.

 Cached volumes offer a substantial cost savings on primary storage and minimize the need to scale your storage on-premises.

#### Scenario

A data analytics company keeps a massive volume of data which they store in their on-premises data center. To scale their storage systems, they are looking for cloud-backed storage volumes that they can mount using Internet Small Computer System Interface (iSCSI) devices from their on-premises application servers. They have an on-site data analytics application which frequently access the latest data subsets locally while the older data are rarely accessed. You are required to minimize the need to scale the on-premises storage infrastructure while still providing their web application with low-latency access to the data.

Which type of AWS Storage Gateway service will you use to meet the above requirements?

--> Cached Volume Gateway

##### Explanation

In this scenario, the technology company is looking for a storage service that will enable their analytics application to frequently access the latest data subsets and not the entire data set because it was mentioned that the old data are rarely being used. This requirement can be fulfilled by setting up a Cached Volume Gateway in AWS Storage Gateway.

## Tape Gateway

VTL -- A virtual tape library

A solution to archive your data in the AWS cloud

* durable
* cost-effective

Tape gateway has a VTL interface that lets you leverage your existing tape-based backup application infrastructure

* Transfer the data on the tapes to virtual tape cartidges that you create on your tape gateway.

If you're using tapes, and applications connect into tapes, you can replace your entire infrastructure and use Tape Gateway instead.

* Add tape cartidges as you need to archive your data.

Tape gateway is preconfigured with:

* media changer
* tape drives
  * available to your existing client backup applications as iSCSI devices

Supported by backup software such as:

* NetBackup
* Backup Exec
* Veeam


