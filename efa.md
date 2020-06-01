# EFA

EFA = Elastic Fabric Adapter.

* For when you need to accelerate HPC and machine learning apps, or if you need to do an OS by-pass.

A network device that you can attach to your EC2 instance.

* accelerates High Performance Computing (HPC) and machine learning applications.

Elastic Fabric Adapter is just a network device that you can attach to your Amazon EC2 instance to accelerate High Performance Computing (HPC) and machine learning applications. EFA enables you to achieve the application performance of an on-premises HPC cluster, with the scalability, flexibility, and elasticity provided by AWS. However, this component is not required in order for your EC2 instance to access the public Internet.

If the scenario involves something regarding EN, and machine learning, think of EFA!

EFA provides (compared to the TCP transport traditionally used in cloud-based HPC systems):

* lower and more consistent latency
* higher throughput 
* OS-bypass
  * enables HPC and machine learning applications to bypass the OS kernal. Communication can occur directly with the EFA device.
  * Makes it faster with less latency.
  * Only supported on Linux currently (not windows).

