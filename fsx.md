# Amazon FSx for Windows File Server
* A native Microsoft Windows file system. 
* Fully-managed
* Makes it easy to move a Windows-based app that requires shared file storage over to AWS.
* Built on Windows Server.
* Runs Windows Server Message Block (**SMB**)-based file services
  * Sample thought process: What storage should you use if you need SMB-based file servers? Choose Windows FSx. 

> Designed for Windows and Windows applications. Provides centralised storage.

Supports:
* AD users
* access control lists
* groups and security policies
* Distributed file System (DFS) namespaces and replication.

Think of FSx for Microsoft specific things, such as:
* Sql-server
* Active directory
* IIS
* Sharepoint
* Any other native Microsoft application

## Compare to EFS
EFS is not SMB based; it's a managed NAS filer for EC2 instances. It's based on Network File System (NFS) version 4.

Choose EFS if you're running linux and you need shared storage. 

# Amazon FSx for Lustre
* Fully-managed file system
* Designed for compute-intensive workloads
  * high-performance computing
  * machine learning
  * media data processing workflows
  * electronic design automation (EDA)
  * financial modeling
  * video processing
* Can process *huuuge* data sets 

> FSx lets you launch and run a Lustre file system

Lustre FSx provides sub-millisecond access to the data, and allows you to read and write data at high speeds. 

FSx for Lustre can also store data *directly on S3*.
