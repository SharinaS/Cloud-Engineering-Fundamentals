# VPN

VPN = Virtual Private Network

Virtual Private Network solutions establish secure connections between your on-premises networks, remote offices, client devices, and the AWS global network. 

By default, instances that you launch into a virtual private cloud (VPC) can't communicate with your own network. You can enable access to your network from your VPC by attaching a virtual private gateway to the VPC, creating a custom route table, updating your security group rules, and creating an AWS managed VPN connection.

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

AWS VPN is comprised of two services: AWS Site-to-Site VPN and AWS Client VPN. Together, they deliver a highly-available, managed, and elastic cloud VPN solution to protect your network traffic.

[See more on AWS](https://aws.amazon.com/vpn/)

## AWS Site-to-Site VPN 

Creates encrypted tunnels between your network and your Amazon Virtual Private Clouds or AWS Transit Gateways.

### Problem

You can’t migrate a database due to customer preferences, so you need to integrate it with your current AWS workload in your VPC in which you are required to establish a site-to-site VPN connection. **What needs to be configured outside of the VPC for you to have a successful site-to-site VPN connection?**

Solution: An Internet-routable IP address (static) of the **customer gateway**'s external interface for the on-premises network.

## AWS Client VPN

For managing remote access, AWS Client VPN connects your users to AWS or on-premises resources using a free VPN software client.

Elastically scales up or down based on user demand.