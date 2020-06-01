# ENI

See below for [EN](#EN)

[AWS doc](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html)

An **elastic network interface** is a logical networking component in a VPC that represents a virtual network card.

* A network card is used by the computer to become part of a network.
* A network card works by taking the data given to it by the CPU and sending it to a destination. It translates the data into a form that can be transferred via cables and then translates the data it receives back into data usable by the computer.

You can create and configure network interfaces in your account and attach them to instances in your VPC.

ENIs let you create low-budget solutions that are also high-availability.

When you provision an EC2 instance, an ENI will be automatically attached. You can then add more as needed.

## Attach and Detach

You can create a network interface, attach it to an instance, detach it from an instance, and attach it to another instance. The attributes of a network interface follow it as it's attached or detached from an instance and reattached to another instance. When you move a network interface from one instance to another, network traffic is redirected to the new instance.

Every instance in a VPC has a default network interface, called the **primary network interface**. You cannot detach a primary network interface from an instance. You can create and attach additional network interfaces. The maximum number of network interfaces that you can use varies by instance type.

### Attachment Types

You can attach a network interface to an instance when it's running (hot attach), when it's stopped (warm attach), or when the instance is being launched (cold attach).

#### Warm Attach example

You want to implement a cost-effective and highly available architecture for your application by launching a standby EC2 instance that is an exact replica of the Windows server. If the primary instance terminates, you can attach the ENI to the standby secondary instance, which allows the traffic flow to resume within a few seconds.

--> Attach an ENI to an instance when it's stopped.

### Instance Failure

If one of your instances serving a particular function fails, its network interface can be attached to a replacement or hot standby instance pre-configured for the same role in order to rapidly recover the service. For example, you can use a network interface as your primary or secondary network interface to a critical service such as a database instance or a NAT instance.

If the instance fails, you (or more likely, the code running on your behalf) can attach the network interface to a hot standby instance. Because the interface maintains its private IP addresses, Elastic IP addresses, and MAC address, network traffic begins flowing to the standby instance as soon as you attach the network interface to the replacement instance. 

Users experience a brief loss of connectivity between the time the instance fails and the time that the network interface is attached to the standby instance, but no changes to the VPC route table or your DNS server are required.

## Network Interface

From AWS: This AWS resource is referred to as a network interface in the AWS Management Console and the Amazon EC2 API. Therefore, we use "network interface" in this documentation instead of "elastic network interface". The term "network interface" in this documentation always means "elastic network interface".

## ENI allows / provides:

* one primary private IPv4 address (from the IPv4 address range of your VPC)
* secondary private IPv4 addresses (from the IPv4 address range of your VPC)
* one Elastic IP address (IPv4) per private IPv4 address
* one public IPv4 address
* IPv6 addresses
* security groups
* one MAC address
* one source/destination check flag
* one description of what the ENI is.

## Where an ENI is found (scenarios):

Basic networking

A management network separate from production network (so, multiple ENIs)

You'd have an additional ENI if you have network and security appliances in your VPC

Having multiple ENIs lets you create dual-homed instances with workloads / roles on distinct subnets. 

* database subnet segregated from production subnet with multiple ENIs, for example.
* You'd have multiple ENI's for each network, essentially.

## Example

Problem:  There is an application on an Amazon EC2 instance in a VPC. Connections are made to the instance using its private IPv4 address. A solutions architect needs to design a solution that will allow traffic to be quickly directed to a standby instance if the application fails and becomes
unreachable.

Solution: Attach a secondary elastic network interface (ENI) to the instance configured with the private IP address. Move the ENI to the standby instance if the primary instance becomes unreachable

# EN 
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

