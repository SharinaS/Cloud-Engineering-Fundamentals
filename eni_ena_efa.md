# ENI vs ENA vs EFA
## ENI
An Elastic Network Interface... a virtual network card for your EC2 instance, essentially.

When you provision an EC2 instance, an ENI will be automatically attached. You can then add more as needed.

ENI allows / provides:
* one primary private IPv4 address (from the IPv4 address range of your VPC)
* secondary private IPv4 addresses (from the IPv4 address range of your VPC)
* one Elastic IP address (IPv4) per private IPv4 address
* one public IPv4 address
* IPv6 addresses
* security groups
* one MAC address
* one source/destination check flag
* one description of what the ENI is.

Where an ENI is found (scenarios):
* Basic networking
* A management network separate from production network (so, multiple ENIs)
* You'd have an additional ENI if you have network and security appliances in your VPC
* Having multiple ENIs lets you create dual-homed instances with workloads / roles on distinct subnets. 
  * database subnet segregated from production subnet with multiple ENIs, for example.
  * You'd have multiple ENI's for each network, essentially.

ENIs let you create low-budget solutions that are also high-availability.


## EN 
Enhanced Networking.
* ENA is a subset of EN.
* Think of when you need speeds between 10Gbps-100Gbps and reliable, high throughput.

Uses single root I/O virtualization (SR-IOV)
* provides high-performance networking abilities on *supported instance types*. 
* SR-IOV is a method of device virtualization that essentially *speeds up your network* - it provides (compared to tranditional virtualized network interfaces):
  * higher I/O performance
  * lower CPU utilization

EN provides:
* higher bandwidth
* higher packet per second (PPS) performance
* lower inter-instance latencies

Sometimes an ENI is not enough, regarding the network throughput you need when doing more hefty workloads. Enter, enhanced networking, for when you need good network performance. So, instead of using many ENI's (which won't necessarily increase your network throughput), use one ENA. 

Cost: No additional charge

### How to enable EN - Two Options: <-- depends on your instance type
* ENA - Elastic Network Adapter
  * Supports network speeds of up to **100Gbps** (gigabits; billions of bits / second; a measure of bandwidth)
* VF - Intel 82599 Virtual Function interface
  * Supports network speeds of up to **10 Gbps**
  * Usually used for older instances, so choose ENA over VF, given the option, especially in exam questions. 

## EFA
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