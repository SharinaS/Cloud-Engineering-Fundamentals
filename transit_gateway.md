# Transit Gateway

AWS Transit Gateway is a service that enables customers to connect their Amazon Virtual Private Clouds (VPCs) and their on-premises networks to a single gateway.

As you grow the number of workloads running on AWS, you need to be able to scale your networks across multiple accounts and Amazon VPCs to keep up with the growth. 

With AWS Transit Gateway, you only have to create and manage a single connection from the central gateway into each Amazon **VPC**, **on-premises data center**, or **remote office** across your network. 

## Hub and Spoke Network

Transit Gateway acts as a hub that controls how traffic is routed among all the connected networks which act like spokes. 

This hub and spoke model significantly simplifies management and reduces operational costs because each network only has to connect to the Transit Gateway and not to every other network. 

Any new VPC is simply connected to the Transit Gateway and is then automatically available to every other network that is connected to the Transit Gateway. 

This ease of connectivity makes it easy to scale your network as you grow.

It acts as a Regional virtual router for traffic flowing between your virtual private clouds (VPC) and VPN connections. A transit gateway scales elastically based on the volume of network traffic. Routing through a transit gateway operates at layer 3, where the packets are sent to a specific next-hop attachment, based on their destination IP addresses.

A transit gateway attachment is both a source and a destination of packets. You can attach the following resources to your transit gateway:

- One or more VPCs
- One or more VPN connections
- One or more AWS Direct Connect gateways
- One or more transit gateway peering connections

If you attach a transit gateway peering connection, the transit gateway must be in a different Region.

### Scenario

A multinational company with multiple on-premises data centers around the globe is heavily using AWS to serve its clients worldwide. The company already has hundreds of VPCs with multiple VPN connections to their data centers that span to multiple AWS Regions. As the number of its workloads running on AWS grows, the company must be able to scale its networks across multiple accounts and Amazon VPCs to keep up. A Solutions Architect is tasked to interconnect all of the company's on-premises networks, VPNs, and VPCs into a single gateway, that includes support for inter-region peering across multiple AWS regions.

Which of the following is the BEST solution that the Architect should set up to support the required interconnectivity?

--> Set up an AWS Transit Gateway to implement a hub-and-spoke network topology in each region that routes all traffic through a network transit center. Route traffic between VPCs and the on-premises data centers over AWS Site-to-Site VPNs.

## Compare with Peering

You can connect pairs of Amazon VPCs using peering. However, managing point-to-point connectivity across many Amazon VPCs, without the ability to centrally manage the connectivity policies, can be operationally costly and cumbersome. 

For on-premises connectivity, you need to attach your AWS VPN to each individual Amazon VPC. This solution can be time consuming to build and hard to manage when the number of VPCs grows into the hundreds.