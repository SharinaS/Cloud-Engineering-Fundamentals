# About Storage Gateway

It's a virtual or physical device that replicates your data into AWS. It's used by businesses/folks to transfer their stuff into AWS.

A service that connects a software appliance that's on-premise with a cloud-based storage.

Goal: Provide seamless and secure integration between on-premises IT environment and AWS' storage infrastructure. 

> Outcome: You can store data in the cloud scalably and cost-effectively. 

There are virtual or physical storage gateways available.

* used to be just a virtual thing
* lately they released hardware applianes to have a physical storage gateway.

## Storage gateway supports:

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

A way to store files directly into S3. For "flat files"

* A way to store your application server data in S3.

Files are stored as objects in S3 buckets. 

Files are accessed through a *Network File System (NFS)* mount point.

> Application Server ----- *via NFS* ----> Storage Gateway ----> Direct Connect / internet / Amazon VPC ------> Amazon S3

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

* Provides low-latency access to the *entire dataset*
* Create storage volumes and mount them as iSCSI devices from your on-premises application servers.
* Data written to your stored volumes is stored on your on-premises hardware.

Stored volumes asynchronously backs up your data to AWS S3.

* Durable, off-site backups
* Backup is stored as Elastic Block Store (EBS) snapshots

> User and client ------> Application server --- *via iSCSI connection* ---> Host, which is running Gateway Virtual machines and creating volumes -----> volumes are replicating over to AWS and stored as snapshots in S3

### Cached Volumes

> Entire dataset is stored on S3, and only the most frequently accessed data is cached on site.

It doesn't deal with the entire dataset locally; it works with the most frequently used data.

Lets you use S3 as your primary data storage, while retaining frequently accessed data locally in your storage gateway.

* Minimizes the need to scale on-premises storage infracture.
* Still provides applications with low-latency access to their frequently accessed data.
* Gateway stores data that you write to these volumes in S3
* Gateway retains recently read data in your on-premises storage gateway's cache and upload buffer storage 

Size:

* Storage volumes up to 32 TiB (terrabytes) and attach them as iSCSI devices from on-premises application servers.

Cached volumes is a way of caching the most actively used data, versus the entire dataset regarding stored volumes.

## Tape Gateway (VTL)

A virtual tape library

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


