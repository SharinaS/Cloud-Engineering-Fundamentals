# CloudFront

A Content Delivery Network (CDN) 

Delivers webpages and other webcontent based on the geographical location of the user, content and the server. 

When the user goes to the website, the user will pull the content down directly from where that website's region is. 

## Definitions

### Edge Location

A location where content will be cached. It is sepearate from a region or availability zone.

They are not just read-only - you can write to them too.

Objects are cached for the life of the TTL (Time to Live).

You can clear cached objects, but you will be charged. 

### Origin 

Origin of all the files that the CDN will distribute. Can be route53, an elastic load balancer, ec2 instance, or s3 bucket.

### Distribution

A collection of edge locations

## How it Works

The user tries to access a website. The website is cached to the edge location if it's not already there. The next user can then access the content.

Can deliver the entire website, including dynamic and streaming content. 

Distributions can be either Web Distribution (typically used for websites) or RTMP (for media streaming - Adobe Flash Media Servers).

CloudFont is a global service - you don't choose a region. 

### To set it up, you "Create a Distribution"

You have the option of choosing "Restrict Bucket Access" - can be useful when using signed URLs. 

Generally can set everything as default. Note that TTL is time to live, as in how long the object will be cached for. TTL is in seconds.

You can restrict access using signed URLs, or signed cookies. Don't have to do this though. 

Can take a while, sometimes up to an hour, to deploy the distribution once the last button is clicked. 

Copy the Domain Name. 

Note that you can "Create Invalidation." This means that the distribution will no longer be in the edge locations. If you push out some data, you figure something is wrong, and in the trouble-shooting steps you can "Create Invalidation."
