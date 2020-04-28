# DNS
DNS is on port 53, so that's where Route53 comes from. 

Must understand dns back to front as a solutions architect. 

DNS is like a phonebook. 

> DNS is used to convert human friendly domain names into an internet protocol (IP). ie, catsareawesome.com --> http//82.124.91.1 <-- IPv4 address

IP addresses are used by computers to identify each other on a network. They come in two types: IPv4 and IPv6. 

### DNS Types (see below for most):
* SOA Records
* NS Records
* A Records
* CNAMES - [see below](#CNAME-Records)
* MX REcords (used for mail)
* PTR Records (way of looking up a name when you have an IP address)

## Health Check
See the section below about [health checks](#Health-Checks).

Key points:
* You can set health checks on individual record sets
* If there is a failure of a record set, it will be taken out of Route53 until it passes the health check. 
* You can set SNS notifications to alert you if a health check has failed. 

## IPv4 and IPv6 Addresses
### IPv4:
A 32-bit field, giving > 4 bill different addresses

Can fill up a dumptruck with sand, if each sand grandule is an address. 

### IPv6: 
Created to deal with running out of IPv4 addresses

Has an address space of 128 bits, which is (theoretically) = 340 undecillion addresses. Can fill up the sun with all the sand granules (a cloud guru).

## ELBs & DNS
Elastic Load Balancers do not have pre-defined IPv4 addresses.

You resolve to them using a *DNS name*. 

## Top Level Domains
bbc.co.uk
* Top level domain - the last word in a domain name - the .uk
* Second level domain - the second word in a domain name - the .co.

Controlled by IANA - Internet Assigned Numbers Authority
* Uses a root zone db to store all available top level domains. 
* http://www.iana.org/domains/root/db

All words in the domain name have to be unique

## Domain Registrars
An authority that can assign domain names directly under 1+ top-level domains.
* *Duplication prevention* overseen by registrars. 
* Domains are registered with InterNIC
  * a service of ICANN
  * ICANN enforces domain name uniqueness across the Internet. 

With Route 53, there is a default limit of 50 domain names one can manage... but you can get the limit increased by contacting AWS support.

### WhoIs database:
A central db where a domain name must get registered. 

### Some Domain Registrars:
Places to go and buy domain names:
* Amazon
* GoDaddy.com
* 123-reg.co.uk

## SOA (Start of Authority) Record
Every DNS address starts with an SOA.

It stores the:
* name of the server that supplied the data for the zone
* the administrator of the zone
* the current version of the data file
* the default number of seconds for the time-to-live (TTL) file on resource records. 

Contains all the DNS records. 

## NS (Name Server) Records
Used by *Top Level Domain servers* to direct traffic to the Content DNS server.

The *Content DNS server* contains the authoritative DNS records

## DNS Records
### A record
Most fundamental type of DNS record.

A = address

A record used by the computer to translate the name of the domain to an IP address!

The A record is a phonebook. 

## Example: 
1. user types into browser, google.com. 

2. The browser goes to the top level domain server and queries it for the authoritative DNS records for the domain.

3. The top level domain server has the (1) Top Level Domain, so Google.com, and (2) the Name Server record.   

4. The NS Records are then queried, which give us the Start of Authority (SOA) record. 

5. The SOA record contains all the DNS records

> user --> top level domain server (authoritative DNS records queried for a name server record) --> Name Server Records (queried for the Start of Authority) --> SOA (queried for DNS records).

## TTL
TTL = Time To Live (in seconds)

The length that a DNS record is cached either... 
* on the Resolving Server, or,
* on the user's local PC

Smaller TTL : Faster the DNS records changes propogate through the internet. 

48 hours is the default TTL. If you change the IP address, it will take 48 hours approx to take effect. 

## CNAME Records
CName = Canonical Name

Found in the Domain Name System (DNS), which is used to map one domain name (an alias) to another (the CName).  

Helpful when running multiple services from a single IP address
* An FTP server and a web server, for eample. 

> CNAME records must point to another domain name (not to an IP address).

**Example** You have created a new subdomain for your website. You want it to point to an ELB using Route53. Use a CNAME record set (not A record set).

## Alias Records
Used to map resource record sets in your hosted zone to:
* Elastic Load Balancers
* CloudFront distributions
* S3 buckets (configured as web sites)

Lets you map one DNS name to another 'target' DNS name.

> Route 53 allows you to create an Alias record at the top node of a DNS namespace (zone apex). 

Alias records provide a Route-52-specific extension to DNS functionality, by being able to be used in the Zone Apex, or point to AWS Resources that are hosted in other accounts (manually enter the ARN to do this). 

### Alias records are like CNAME records... but different: 
* Both let you map a DNS name to another target name
* Difference: CNAME cannot be used for naked domain names
  * ie, can't have a CNAME for http://acloud.guru... it has to be either an *A record* or an *Alias*. 
  * ie, when you're trying to point your naked domain name to an EC2 instance, you must use an alias record (and not a CNAME)
  * CNAME records will *not* let you create an Alias record at the top node of a DNS namespace.

Naked domain names are aka Zone Apex Record.
* The entire domain minus the www infront of it. 

# Routing Policies Available with Route53
## Simple Routing Policy:
> Can only have one record with multiple IP addresses. There are no health checks possible.

If you specify multiple values in a record, Route53 returns all values in a random order. 

User --> DNS request to Route53 --> Route53 has 2 IP addresses (30.0.0.0.1 and 30.0.0.0.2)
* Route53 will pick the IP addresses at random.

Can change the TTL (lower it) to have increased speed of randomization. 

[Set up Simple Routing](#Set-up-Simple-Routing)

## Weight Routing Policy:
Allows you to split your traffic based on different weights assigned. 

It adds up all the weights in the record set, and apportions them off appropriately. 

You can have 10% of your traffic go to us-east-1, and 90% of traffic go to eu-west-1. 

User types domain name into browser --> DNS request to Route53 --> Route53 sends 30% traffic to us-east-1 **+** Route53 sends 70% traffic to us-west-2. 

[Set up Weighted Routing](#Set-up-Weighted-Routing)

## Latency-based Routing:
Lets you route your traffic based on the lowest network latency for your end user, as in whichever region will give them the fastest response time. 

To use it, you make a latency resource record set for the EC2 (or ELB) resource in each region that hosts your website. When Route53 gets a query for your site, it chooses the "latency resource record set" for the region that will provide the user with the lowest latency. Route53 will then respond with the value affiliated with that particular resource record set. 

User makes request --> Route53 analyzes the different response times for each of the regions it's associated with --> Route53 sends traffic to the the region that has lower latency. 

[Set up Latency Routing](#Set-up-Latency-Routing)

## Failover Routing Policy
> Used for when you want an active-passive setup, where you have a primary site and a secondary site.

One site could be eu-west-2 and the secondary disaster recovery (DR) site in us-west-2.

Route53 will monitor the health of the primary site with a health check of its endpoints.

User does a DNS request --> Route53 will send traffic to the active region, vs the passive region... unless the active region fails, and then traffic is automatically routed to the passive region. 

[Set up Failover Routing in the AWS Console](#Set-up-Failover-Routing)

## Geolocation Routing Policy
Lets you choose where your traffic will be sent based on the geographic locations of your users.
* The location from which DNS queries originate
* Has nothing to do with latency.
* Based on *national boundaries*

**Example:** All queries from the US are routed to a fleet of EC2 instances that are specifically configured for your American customers. These servers have the language set for English, and all prices are displayed in dollars. 

US customer does a DNS request --> Route53 routes the traffic to us-west-2.

European customer does a DNS request --> Route53 routes the traffic to eu-west-1

**Example:** All queries from Europe get routed to an ELB in Frankfurt. 

[Set up Geolocation Routing in the Console](#Set-up-Geolocation-Routing)

## Geoproximity Routing (Traffic Flow Only)
Route53 traffic routes traffic to your resources based on the geographic location of your users *and* your resources. 
* Based on *latitude and longitude*.

You can also optionally choose to reoute more or less traffic to a given resource by choosing a *bias*, which is a value. 
* A **bias** increases or decreases the size of the geographic region that traffic is being routed from (over to a resource). 

> You must use Route 53 traffic flow in order to use geoproximity routing. 

[Set up some things, below](#Set-up-Geoproximity-Routing)

## Multivalue Answer Routing
> The same as [Simple Routing](#Simple-Routing-Policy) with the additional perk of putting health checks on each record set.

You can choose multiple values for almost any record + check the health of each resource. 

Route 53 is able to be configured to return multiple values (ie, IP addresses for your web servers), in response to DNS queries. 

User connects into Route53 --> traffic sent to the first route, unless it is compromised, at which point it will be sent to the second route, etc.

**Example**: An ideal choice for a company that is hosting, say, 10 web servers, all with the same content, which wants to serve traffic to random servers, but also have the best resiliency. (choose Multivalue over Simple Routing)

[See below for more](#Set-up-Multivalue-Answer-Policy)

-------
# Do the Things

## Register a Domain Name

> AWS Console > Networing & Content Delivery > Route53

Click on the "Get started now" for "Domain registration"

Click "Register Domain" and choose a name to buy. Click "Continue"

Fill out the Registrant Contact form, and hit "Continue"

> Domain registration can take up to 3 days to complete... but it ususally takes 1-2 hours. 

After registration finishes up, you can see your "Hosted zones" in the nav bar on the left (Domains > Registered domains).

Click on "Hosted zones," and in the new window, click on the radio button for the domain name. Can eventually configure Route53 (see below) from here. 

## Set up Some EC2 Instances to Explore Record Sets:
Set up an instance that is close to your region, one that is far from you, and one that is someplace in between (3 instances).
* You'll have to set up a public security group and get a keypair if you haven't used one of these regions before.  

Add some user data to install Apache

```bash
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>Hello from the $(curl http://169.254.169.254/latest/meta-data/instance-id) instance!</h1><html>" > index.html
```
## Set up Simple Routing:
Copy the IPv4 IP addresses of the instances you set up. 

Go back to Route53 > Hosted zones

With the radio button checked of your domain name, click on "Go to Record Sets" above. 

In the new window, click the button that says "Create Record Set"

On the right, a window will appear to Create a Record Set.

Leave the domain name as a naked domain name

You can click on Type to see the pulldown menu of all the different types of records. It should be set to A- IPv4 address.
* To the left, in Type NS, you can see that Amazon gives you top level domains - .uk, .org, .net, .com - in case one goes down. 

Paste in the IPv4 addresses you copied in the "Values" text box. 

Hit "Create"

### Test it:

Go to a browser window and type in the domain name you registered. Only one of the instances will appear, even after refreshing.
* The browser has cached an IP address to the domain name. 

Go back to Route53, and with the radio button for the domain name checked, you can "Edit Record Set" (window on the right). 

Lower the TTL to +1m. Click "Save Record Set" (it will take about 6 min). 

Now, can refresh your website and after a minute, it will change to the next instance. 

Can delete the A record by clicking the radio button and then clicking  

## Set up Weighted Routing
Networking & Content Delivery > Route53 > Dashboard > Hosted zones 

Click "Create Record Set"

In the window on the right, paste in one of your IP addresses. 

Change the Routing Policy to "Weighted."

Give a "Weight" of some (integer) percentage, like 20. 

Add a Set ID, which is a name of your choosing. 

Click "Create."

Do the same thing again, for every IP address you have.

### Health Checks
> If a record fails a health check, it will be removed from Route53 until it passes the health check. 

You can click the radio button, "Yes" to "Associate with Health Check," which will tell Route53 to remove that instance from the DNS if that instance fails.

You have to set up Health checks to do this first though - *for each instance*.

Click on "Health checks" on the left nav bar. Click on the button "Create health check"

Give it a name. We want it to monitor an endpoint.

For "Specify endpoint by" click "IP address," and below it, type in an IP address. 

For "Host name," type in the name of the domain (google.com, for ex). 

Port 80 is fine to leave, since this is a webserver, and Path can be the index.html of the webpage (though double check this, since with a loadbalancer, "/" can be more ideal). 

Click Next.

In real life, click "Yes" that you want to be notified for when health checks fail.  

Create health checks for each of your other instances, and associate those health checks with the approriate regions. 

> If you set up an IP address endpoint, and the instance was stopped and then started, the public IP address will *change!* 

You'll either need to manually change the IP address endpoint, which means updating your health check(s)... or set up a **dedicated IPv4 address**... or use an **Elastic Load Balancer** with a DNS name associated with the health check :) 

## Set up Latency Routing
"Create Record Set" button. Can use your naked domain name. 

Paste an IP address into the "Value" box. 

Change Routing Policy to "Latency."

For Region, it will automatically detect the region that the IP address is in. Give it a Set ID, or a name of your choosing. 

Click "Yes" to associate it with a health check, if you like. Use the pulldown menu to associate it with a health check you've made already, or make a new health check (see [above](#Health-Checks)).

Click "Create."

Do this for each IP address you have. 

**Testing:** You can start up a VPN client, and choose various locations, to see how the latency routing responds :) Just hit refresh each time you change locations via the VPN client.

## Set up Failover Routing
[More about Failover Routing, above](#Failover-Routing-Policy)

Click button for "Create Record Set"

Paste in the IP address you want as a primary.

In "Routing Policy," click "Failover," and choose the Record Type of "Primary."

Associate it with a health check in the appropriate region. To make a new health check, see [above](#Health-Checks).

Make a second record set, and make this the secondary one. You don't have to associate this one with a health check, or you can if you want. Click "Create." 

You may need to drop your TTLs to 60 seconds. 

**Testing:** Stop one of your instances, then about 5 minutes later, check the health check, and refresh your domain name in the browser.

## Set up Geolocation Routing
[More about Geolocation Routing, above](#Geolocation-Routing-Policy)

Click on the "Create Record Set" button. Use the Zone Apex, you can drop the TTL to +1m (60 sec).

Paste in the IP address associated with the region you want.

For routing policy, change it to "Geolocation" - you can choose continents or countries.

Give it a logical name as the SetId. 

Associate it with a pre-made health check (or [make a new one](#Health-Checks)). If you choose Europe, you could associate it with a health check made for Ireland, for example.

Make more Record Sets, for each IP address.

For the region of Ohio, you could choose North America as the Location, for example. 

**Testing:** Use a VPN to change locations and watch the browser pull up website stuff for each server that is appropriate for the geolocation. 

Delete the records when you're done with them.

## Set up Geoproximity Routing
On the left nav bar, you should see Traffic Flow > Traffic policies. Click on "Create traffic policy."

Give it a name and a description.

Create a traffic policy - you can import one, or you can build it in the console:
* Choose DNS type as "A:IP address in IPv4 format"
* Choose Connect to: "Geoproximity rule" ... and create wildly complicated traffic rules like a network engineer... if you dare. 

More than knowing just the basic potential of this routing option is not required for the Solutions Architect exam. 

## Set up Multivalue Answer Policy
Networking & Content Delivery > Route53 > Hosted Zones > your domain name

"Create Record Set" 

Paste in a *single* IP Address (versus all of them, which we did with the Simple Routing Policy)

Change Routing Policy to "Multivalue Answer."

Add a Set ID as the name of it. 

You can now associate it with a health check that you've created. 

Click "Create." 

Do this for your other Instances. You'll be able to associate each instance with its own health check.

> When one server fails (or is terminated), traffic for the website will be sent to one of the other instances. 

[More, above](#Multivalue-Answer-Routing)