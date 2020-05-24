# EFA

Elastic Fabric Adapter.
* For when you need to accelerate HPC and machine learning apps, or if you need to do an OS by-pass.

A network device that you can attach to your EC2 instance.
* accelerates High Performance Computing (HPC) and machine learning applications.

If the scenario involves something regarding EN, and machine learning, think of EFA!

EFA provides (compared to the TCP transport traditionally used in cloud-based HPC systems):
* lower and more consistent latency
* higher throughput 
* OS-bypass
  * enables HPC and machine learning applications to bypass the OS kernal. Communication can occur directly with the EFA device.
  * Makes it faster with less latency.
  * Only supported on Linux currently (not windows).