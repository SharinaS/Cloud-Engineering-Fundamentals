# VPN

VPN = Virtual Private Network

Virtual Private Network solutions establish **secure connections** between your on-premises networks, remote offices, client devices, and the AWS global network. 

By default, instances that you launch into a virtual private cloud (VPC) can't communicate with your own network. You can enable access to your network from your VPC by attaching a **virtual private gateway** to the VPC, creating a custom **route table**, updating your **security group** rules, and creating an AWS **managed VPN** connection.

Although the term VPN connection is a general term, in the Amazon VPC documentation, a VPN connection refers to the connection between your VPC and your own network. 

AWS supports Internet Protocol security (IPsec) VPN connections.

# Components of a VPN Connection

1. Virtual Private Gateway
2. Customer Gateway
    * A customer gateway is a physical device or software application on your side of the VPN connection.

![diagram](/assets/vpn.jpg)

* diagram from Udemy's AWS Certified Solutions Architect Associate Practice Tests

## To enable access to your network from your VPC

1. attach a virtual private gateway to the VPC
2. create a custom route table
3. update your security group rules
4. create an AWS managed VPN connection.

## To create a VPN connection, you must 

1. create a customer gateway resource in AWS, which provides information to AWS about your customer gateway device. 
2. Next, you have to set up an Internet-routable IP address (static) of the customer gateway's external interface.

# Two Services

AWS VPN is comprised of two services: 

* AWS Site-to-Site VPN 
* AWS Client VPN. 

Together, they deliver a highly-available, managed, and elastic cloud VPN solution to protect your network traffic.

[See more on AWS](https://aws.amazon.com/vpn/)

## AWS Site-to-Site VPN 

Creates encrypted tunnels between your network and your Amazon Virtual Private Clouds or AWS Transit Gateways.

An AWS Site-to-Site VPN connection connects your VPC to your datacenter. Amazon supports Internet Protocol security (IPsec) VPN connections. Data transferred between your VPC and datacenter routes over an encrypted VPN connection to help maintain the confidentiality and integrity of data in transit. An Internet gateway is not required to establish a Site-to-Site VPN connection.

### Connectivity Options for VPC

Connect your VPC to your corporate data center using a Hardware VPN connection via the virtual private gateway.

### Accessing the Internet without public IP Addresses

For VPCs with a **hardware VPN** connection or [Direct Connect](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/direct_connect.md) connection, instances can route their Internet traffic down the virtual private gateway to your existing datacenter. From there, it can access the Internet via your existing egress points and network security/monitoring devices.

(Otherwise, instances without public IP addresses can route their traffic through a network address translation (NAT) gateway or a NAT instance to access the internet. These instances use the public IP address of the NAT gateway or NAT instance to traverse the internet. The NAT gateway or NAT instance allows outbound communication but doesn’t allow machines on the internet to initiate a connection to the privately addressed instances.)

### Problem

You can’t migrate a database due to customer preferences, so you need to integrate it with your current AWS workload in your VPC in which you are required to establish a site-to-site VPN connection. **What needs to be configured outside of the VPC for you to have a successful site-to-site VPN connection?**

Solution: An Internet-routable IP address (static) of the **customer gateway**'s external interface for the on-premises network.

[See more on AWS](https://aws.amazon.com/vpn/faqs/)

## AWS Client VPN

For managing remote access, AWS Client VPN connects your users to AWS or on-premises resources using a free VPN software client.

A VPN allows you to connect your AWS cloud resources to your on-premises data center using secure and private sessions with IP Security (IPSec) or Transport Layer Security (TLS) tunnels... since one of the main advantages of having a VPN connection is that you will be able to connect your Amazon VPC to other remote networks securely.

In other words, AWS Site-to-Site VPN establishes secure and private sessions with IP Security (IPSec) and Transport Layer Security (TLS) tunnels.

Elastically scales up or down based on user demand.

### Enable Connectivity to Other Networks

You can enable connectivity to other networks like peered Amazon VPCs, on-premises networks via virtual gateway or AWS services, such as S3, via endpoints, networks via AWS PrivateLink or other resources via internet gateway. 

To enable connectivity, add a route to the specific network in the Client VPN route table, and add authorization rule enabling access to the specific network.

### Compare with Direct Connect

Direct Connect enables you to establish a private and dedicated network connection between your network and your VPC.

[See more on AWS](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/direct_connect.md)
