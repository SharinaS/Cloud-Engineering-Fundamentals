# CloudFront

## Index (in the works)

* [Edge Location](#Edge-Location)
* [Lambda@Edge](#Lambda@Edge)
* [Origin Failover](#Origin-Failover)
* [Perfect Forward Secrecy](#Perfect-Forward-Secrecy)
* [Signed URLS and Signed Cookies](#Signed-URLS-and-Signed-Cookies)
* [SNI](#SNI)

# About

A Content Delivery Network (CDN).

Amazon CloudFront is a fast content delivery network (CDN) service that securely delivers **data, videos, applications, and APIs** to customers globally with low latency, high transfer speeds, all within a developer-friendly environment.

> Thus, application origins don’t need to scale to meet demands for static content.

Delivers webpages and other webcontent based on the geographical location of the user, content and the server.

When the user goes to the website, the user will pull the content down directly from where that website's region is.

# Use Cases

## Accelerate static website content delivery.

CloudFront can speed up the delivery of your static content (for example, images, style sheets, JavaScript, and so on) to viewers across the globe. By using CloudFront, you can take advantage of the AWS backbone network and CloudFront edge servers to give your viewers a fast, safe, and reliable experience when they visit your website.

## Live & on-demand video streaming.

The Amazon CloudFront CDN offers multiple options for streaming your **media** – both pre-recorded files and live events – at sustained, high throughput required for 4K delivery to global viewers.

# CloudFront Origin

CloudFront works seamlessly with services including 

- AWS Shield for DDoS mitigation
- Amazon S3
- Elastic Load Balancing
- Amazon EC2 as origins for your applications

If you use AWS origins such as **Amazon S3, Amazon EC2 or Elastic Load Balancing**, you don’t pay for any data transferred between these services and CloudFront.

# Lambda@Edge

Customizable content delivery.

Lambda@Edge is a feature of Amazon CloudFront that lets you run code closer to users of your application, which improves performance and reduces latency.

## Functions Run Responsively to Events

Lambda@Edge lets you run **Lambda functions** to customize the content that CloudFront delivers, executing the functions in AWS locations closer to the viewer. The functions run in response to CloudFront events, without provisioning or managing servers. 

You can use Lambda functions to change CloudFront requests and responses at the following points:

- After CloudFront receives a request from a viewer (viewer request)

- Before CloudFront forwards the request to the origin (origin request)

- After CloudFront receives the response from the origin (origin response)

- Before CloudFront forwards the response to the viewer (viewer response)

## Quicker Authentication

you can use Lambda@Edge to allow your Lambda functions to customize the content that CloudFront delivers and to execute the authentication process in AWS locations closer to the users. 

# Edge Location

A location where content will be cached. It is separate from a region or availability zone.

> They are not just read-only - you can write to them too.

Objects are cached for the life of the TTL (Time to Live).

You can clear cached objects, but you will be charged. 

## Distribution

A collection of edge locations

# How it Works

The user tries to access a website. The website is cached to the edge location if it's not already there. The next user can then access the content.

Can deliver the entire website, including dynamic and streaming content. 

Distributions can be either Web Distribution (typically used for websites) or RTMP (for media streaming - Adobe Flash Media Servers).

CloudFont is a global service - you don't choose a region.

# Origin Failover 

...for Performance.

Set up an **origin failover** by creating an **origin group** with two origins, with one as the primary origin and the other as the second origin which CloudFront automatically switches to when the primary origin fails. To set up origin failover, you must have a distribution with at least two origins.

This will alleviate occasional **HTTP 504 errors** users may experience.

## Cheaper

Much cheaper than if you use multiple regions and Route53 if you deploy your application to multiple AWS regions to accommodate your users around the world, and if you also set up a Route 53 record with latency routing policy to route incoming traffic to the region that provides the best latency to the user.

## Use with Lambda@Edge Functions

The Lambda function is triggered for the primary origin when CloudFront routes a request to it on a cache miss. If the primary origin returns an HTTP status code that you’ve configured for failover, the Lambda function is triggered again, when CloudFront re-routes the request to the second origin.

[More in the Developers Guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html).

# Security

CloudFront integrates seamlessly with AWS Shield for Layer 3/4 DDoS mitigation and AWS WAF for Layer 7 protection.

## Perfect Forward Secrecy

Perfect Forward Secrecy is a feature that provides additional safeguards against the **eavesdropping of encrypted data**, through the use of a unique **random session key**. This prevents the decoding of captured data, even if the secret long-term key is compromised.

Perfect Forward Secrecy is used to offer SSL/TLS cipher suites.

CloudFront and Elastic Load Balancing are the two AWS services that support Perfect Forward Secrecy.

# Signed URLS and Signed Cookies

CloudFront signed URLs and signed cookies provide the same basic functionality: they allow you to **control who can access your content**. For if you want to serve private content through CloudFront. 

## Use signed URLs for the following cases:

* You want to use an RTMP distribution. 
  * RTMP distributions stream media files using Adobe Media Server and the Adobe Real-Time Messaging Protocol (RTMP)
  * Signed cookies aren't supported for RTMP distributions.

- You want to **restrict access to individual files**, for example, an installation download for your application.

- Your users are using a client (for example, a custom HTTP client) that doesn't support cookies.

## Use signed cookies for the following cases:

* You want to **provide access to multiple restricted files**, for example, all of the files for a video in HLS format or all of the files in the subscribers' area of a website.
  * HLS, aka HTTP Live Streaming, is an HTTP-based adaptive bitrate streaming communications protocol (see more on [wikipedia](https://en.wikipedia.org/wiki/HTTP_Live_Streaming))
* You don't want to change your current URLs.

# SNI

## Transport Layer Security protocol

"Transport Layer Security (TLS), and its now-deprecated predecessor, Secure Sockets Layer (SSL), are cryptographic protocols designed to provide communications security over a computer network." - Wikipedia

## Server Name Indication

SNI = Server Name Indication

SNI Custom SSL relies on the SNI extension of the Transport Layer Security protocol, which allows multiple domains to serve SSL traffic over the **same IP address** by including the hostname which the viewers are trying to connect to.

Amazon CloudFront delivers your content from each edge location and offers the same security as the Dedicated IP Custom SSL feature. 

### Browsers

SNI Custom SSL works with most modern browsers, including Chrome version 6 and later (running on Windows XP and later or OS X 10.5.7 and later), Safari version 3 and later (running on Windows Vista and later or Mac OS X 10.5.6. and later), Firefox 2.0 and later, and Internet Explorer 7 and later (running on Windows Vista and later).

### Downside

Some users may not be able to access your content because some older browsers do not support SNI and will not be able to establish a connection with CloudFront to load the HTTPS version of your content. If you need to support non-SNI compliant browsers for HTTPS content, it is recommended to use the Dedicated IP Custom SSL feature.

### Fees

There are no additional certificate management fees to SNI; you simply pay normal Amazon CloudFront rates for data transfer and HTTPS requests.

### Scenario

A web application is hosted in an Auto Scaling group of EC2 instances behind a Classic Load Balancer. You need to secure your application by allowing multiple domains to serve SSL traffic over the same IP address.

Solution: Generate an SSL certificate with AWS Certificate Manager and create a CloudFront web distribution. Associate the certificate with your web distribution and enable the support for Server Name Indication (SNI).

### Application Load Balancer

You can host multiple TLS secured applications, each with its own TLS certificate, behind a single load balancer. In order to use SNI, all you need to do is bind multiple certificates to the same secure listener on your load balancer. ALB will automatically choose the optimal TLS certificate for each client. These features are provided at no additional charge.

#### Scenario

A travel company has a suite of web applications hosted in an Auto Scaling group of On-Demand EC2 instances behind an Application Load Balancer that handles traffic from various web domains such as i-love-manila.com, i-love-boracay.com, i-love-cebu.com and many others. To improve security and lessen the overall cost, you are instructed to secure the system by allowing multiple domains to serve SSL traffic without the need to reauthenticate and reprovision your certificate everytime you add a new domain. This migration from HTTP to HTTPS will help improve their SEO and Google search ranking.

Which of the following is the most cost-effective solution to meet the above requirement?

--> Upload all SSL certificates of the domains in the ALB using the console and bind multiple certificates to the same secure listener on your load balancer. ALB will automatically choose the optimal TLS certificate for each client using Server Name Indication (SNI).

You could also create a new CloudFront web distribution and configure it to serve HTTPS requests using dedicated IP addresses in order to associate your alternate domain names with a dedicated IP address in each CloudFront edge location... but this is *not cost effective!*

# Console Demo

To set it up, you create a 'Distribution.'

You have the option of choosing "Restrict Bucket Access" - can be useful when using signed URLs. 

Generally can set everything as default. Note that TTL is time to live, as in how long the object will be cached for. TTL is in seconds.

You can restrict access using signed URL, or signed cookies. Don't have to do this though. 

Can take a while, sometimes up to an hour, to deploy the distribution once the last button is clicked. 

Copy the Domain Name. 

Note that you can "Create Invalidation." This means that the distribution will no longer be in the edge locations. If you push out some data, you figure something is wrong, and in the trouble-shooting steps you can "Create Invalidation."
