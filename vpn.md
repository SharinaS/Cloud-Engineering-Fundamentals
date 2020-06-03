# VPN

## Index

[Components of a VPN Connection](#Components-of-a-VPN-Connection)

# About VPNs

VPN = Virtual Private Network

Virtual Private Network solutions establish **secure connections** between your on-premises networks, remote offices, client devices, and the AWS global network. 

By default, instances that you launch into a virtual private cloud (VPC) can't communicate with your own network. You can enable access to your network from your VPC by attaching a **virtual private gateway** to the VPC, creating a custom **route table**, updating your **security group** rules, and creating an AWS **managed VPN** connection. See what is normally created with a VPC [in my VPC doc](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/vpc.md#What-is-Created-with-a-VPC).

Although the term VPN connection is a general term, in the Amazon VPC documentation, a VPN connection refers to **the connection between your VPC and your own network**. 

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

1. create a **customer gateway** resource in AWS, which provides information to AWS about your customer gateway device. 
2. Next, you have to set up an **Internet-routable IP** address (static) of the customer gateway's external interface.

# Two Services

AWS VPN is comprised of two services: 

* AWS Site-to-Site VPN 
* AWS Client VPN. 

Together, they deliver a highly-available, managed, and elastic cloud VPN solution to protect your network traffic.

[See more on AWS](https://aws.amazon.com/vpn/)

## AWS Site-to-Site VPN 

[See the user guide on AWS](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html).

Creates encrypted tunnels between your network and your Amazon Virtual Private Clouds or AWS Transit Gateways.

> Site-to-Site VPNs is characterized by remote connections between entire networks.

An AWS Site-to-Site VPN connection connects your VPC to your datacenter. 


An Internet gateway is not required to establish a Site-to-Site VPN connection.

### Global Acceleration

For globally distributed applications, the Accelerated Site-to-Site VPN option provides even greater performance by working with AWS Global Accelerator.

### Connectivity Options for VPC

Connect your VPC to your corporate data center using a Hardware VPN connection via the virtual private gateway.

### High Availability

AWS Site-to-Site VPN delivers high availability by using two tunnels across multiple Availability Zones within the AWS global network. You can stream primary traffic through the first tunnel and use the second tunnel for redundancy — if one tunnel goes down, traffic continues to flow.

On the AWS side of the VPN connection, a virtual private gateway provides the VPN endpoints (tunnels) for automatic failover.

### Security

Data transferred between your VPC and datacenter routes over an encrypted VPN connection to help maintain the confidentiality and integrity of data in transit. 

You can connect to an Amazon VPC or AWS Transit Gateway the same way you connect to your on-premises servers.

AWS Site-to-Site VPN establishes secure and private sessions with IP Security (IPSec) and Transport Layer Security (TLS) tunnels.

### Failover

To protect against a loss of connectivity in case your customer gateway device becomes unavailable, you can set up a second Site-to-Site VPN connection to your VPC and virtual private gateway by using a second customer gateway device. By using redundant Site-to-Site VPN connections and customer gateway devices, you can perform maintenance on one of your devices while traffic continues to flow over the second customer gateway's Site-to-Site VPN connection.

1. Set up a second Site-to-Site VPN connection by using the same virtual private gateway and creating a new customer gateway. The customer gateway IP address for the second Site-to-Site VPN connection must be publicly accessible.
2. Configure a second customer gateway device. Both devices should advertise the same IP ranges to the virtual private gateway. We use BGP routing to determine the path for traffic. If one customer gateway device fails, the virtual private gateway directs all traffic to the working customer gateway device.

### Access the Internet without public IP Addresses

For VPCs with a **hardware VPN** connection or [Direct Connect](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/direct_connect.md) connection, instances can route their Internet traffic down the virtual private gateway to your existing datacenter. From there, it can access the Internet via your existing egress points and network security/monitoring devices.

(Otherwise, instances without public IP addresses can route their traffic through a network address translation (NAT) gateway or a NAT instance to access the internet. These instances use the public IP address of the NAT gateway or NAT instance to traverse the internet. The NAT gateway or NAT instance allows outbound communication but doesn’t allow machines on the internet to initiate a connection to the privately addressed instances.)

### Scenario

You can’t migrate a database due to customer preferences, so you need to integrate it with your current AWS workload in your VPC in which you are required to establish a site-to-site VPN connection. **What needs to be configured outside of the VPC for you to have a successful site-to-site VPN connection?**

Solution: An Internet-routable IP address (static) of the **customer gateway**'s external interface for the on-premises network.

[See more on AWS](https://aws.amazon.com/vpn/faqs/)

### Compare with Direct Connect

Direct Connect enables you to establish a private and **dedicated** network connection between your network and your VPC.

[See my Direct Connect doc](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/direct_connect.md)


## AWS Client VPN

For **managing remote access**, AWS Client VPN connects your users to AWS or on-premises resources using a free VPN software client.

AWS Client VPN is a fully-managed, elastic VPN service that automatically scales up or down based on user demand.

It enables you to access your AWS resources and on-premises resources using a single VPN tunnel.

> Client-to-Site VPN is characterized by single user connections.

[AWS Admin Guide](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/what-is.html).

### Elasticity

Traditional on-premises VPN services are limited by the capacity of the hardware that runs them. AWS Client VPN is a pay-as-you-go cloud VPN service that elastically scales up or down based on user demand.

### Remote Access

Unlike on-premises VPN services, AWS Client VPN allows users to connect to AWS and on-premises networks using a single VPN connection

