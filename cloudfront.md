# CloudFront

## Index (in the works)



# About
A Content Delivery Network (CDN) 

Delivers webpages and other webcontent based on the geographical location of the user, content and the server.

When the user goes to the website, the user will pull the content down directly from where that website's region is.

# Use Cases

From *AWS Certified Cloud Practitioner Practice Exam Course*:

## Accelerate static website content delivery.

CloudFront can speed up the delivery of your static content (for example, images, style sheets, JavaScript, and so on) to viewers across the globe. By using CloudFront, you can take advantage of the AWS backbone network and CloudFront edge servers to give your viewers a fast, safe, and reliable experience when they visit your website.

## Live & on-demand video streaming.

The Amazon CloudFront CDN offers multiple options for streaming your media – both pre-recorded files and live events – at sustained, high throughput required for 4K delivery to global viewers.

# Security

CloudFront integrates seamlessly with AWS Shield for Layer 3/4 DDoS mitigation and AWS WAF for Layer 7 protection.

# Lambda@Edge

Customizable content delivery.

Lambda@Edge is a feature of Amazon CloudFront that lets you run code closer to users of your application, which improves performance and reduces latency.

## Functions Run Responsively to Events

Lambda@Edge lets you run Lambda functions to customize the content that CloudFront delivers, executing the functions in AWS locations closer to the viewer. The functions run in response to CloudFront events, without provisioning or managing servers. 

You can use Lambda functions to change CloudFront requests and responses at the following points:

- After CloudFront receives a request from a viewer (viewer request)

- Before CloudFront forwards the request to the origin (origin request)

- After CloudFront receives the response from the origin (origin response)

- Before CloudFront forwards the response to the viewer (viewer response)

## Quicker Authentication

you can use Lambda@Edge to allow your Lambda functions to customize the content that CloudFront delivers and to execute the authentication process in AWS locations closer to the users. 


## Origin Failover for Performance Issues

In addition, you can set up an origin failover by creating an **origin group** with two origins, with one as the primary origin and the other as the second origin which CloudFront automatically switches to when the primary origin fails. 

This will alleviate occasional **HTTP 504 errors** users may experience.

Much cheaper than if you use multiple regions and Route53, for example, if you deploy your application to multiple AWS regions to accommodate your users around the world, and if you also set up a Route 53 record with latency routing policy to route incoming traffic to the region that provides the best latency to the user.

# Definitions

## Edge Location

A location where content will be cached. It is separate from a region or availability zone.

> They are not just read-only - you can write to them too.

Objects are cached for the life of the TTL (Time to Live).

You can clear cached objects, but you will be charged. 

## CloudFront Origin

Origin of all the files that the CDN will distribute. 

Can be: 

* Route53
* an elastic load balancer, 
* an EC2 instance
* an S3 bucket.

## Distribution

A collection of edge locations

# How it Works

The user tries to access a website. The website is cached to the edge location if it's not already there. The next user can then access the content.

Can deliver the entire website, including dynamic and streaming content. 

Distributions can be either Web Distribution (typically used for websites) or RTMP (for media streaming - Adobe Flash Media Servers).

CloudFont is a global service - you don't choose a region.

# Signed URLS and Signed Cookies

CloudFront signed URLs and signed cookies provide the same basic functionality: they allow you to control who can access your content. If you want to serve private content through CloudFront and you're trying to decide whether to use signed URLs or signed cookies, consider the following:

## Use signed URLs for the following cases:

- You want to use an RTMP distribution. Signed cookies aren't supported for RTMP distributions.
  * RTMP distributions stream media files using Adobe Media Server and the Adobe Real-Time Messaging Protocol (RTMP)

- You want to restrict access to **individual files**, for example, an installation download for your application.

- Your users are using a client (for example, a custom HTTP client) that doesn't support cookies.

## Use signed cookies for the following cases:

- You want to provide access to **multiple restricted files**, for example, all of the files for a video in HLS format or all of the files in the subscribers' area of a website.
  * HTTP Live Streaming (also known as HLS) is an HTTP-based adaptive bitrate streaming communications protocol (see more on [wikipedia](https://en.wikipedia.org/wiki/HTTP_Live_Streaming))

- You don't want to change your current URLs.

# To set it up, you "Create a Distribution"

You have the option of choosing "Restrict Bucket Access" - can be useful when using signed URLs. 

Generally can set everything as default. Note that TTL is time to live, as in how long the object will be cached for. TTL is in seconds.

You can restrict access using signed URL, or signed cookies. Don't have to do this though. 

Can take a while, sometimes up to an hour, to deploy the distribution once the last button is clicked. 

Copy the Domain Name. 

Note that you can "Create Invalidation." This means that the distribution will no longer be in the edge locations. If you push out some data, you figure something is wrong, and in the trouble-shooting steps you can "Create Invalidation."
