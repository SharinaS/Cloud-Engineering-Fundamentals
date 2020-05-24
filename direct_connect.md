# Direct Connect

A direct connection from your data center into AWS, using dedicated lines. It is an AWS service. 

Allows for private connectivity between AWS and a datacenter, office, etc. 

Direct connect locations are spread all over the world. 

Within direct connect is a "AWS cage," which has routers. Customers routers then connect to the routers within the AWS cage. None of this traverses the internet. There is use of the AWS backbone network. 

Useful for high throughput workloads (ie lots of network traffic). 

Useful for when you need a stable and reliable and secure connection. For example, if you have situation where you're trying to use a VPN connection but it keeps dropping out b/c of the amount of throughput, you can use a direct connect to solve those issues. 

## Steps for Setup

Note that if one does not have a direct connect connection at your location, one can't do these steps. 

1. Create a virtual interface in the Direct Connect console - this is a Public Virtual Interface.
2. Go to the VPC console > VPN connections. Create a Customer Gateway.
3. Create a Virtual Private Gateway
4. Attach the Virtual Private Gateway to the desired VPC.
5. Select VPN Connections and create new VPN Connection
6. Select the Virtual Private Gateway and the Customer Gateway (both of which make up a VPN connection)
7. Once the VPN is available, set up the VPN on the customer gateway or firewall.

^-- memorize these steps for the exam and watch this video on YouTube, called [How do I configure a VPN over AWS Direct Connect?](https://www.youtube.com/watch?v=dhpTTT6V1So). 