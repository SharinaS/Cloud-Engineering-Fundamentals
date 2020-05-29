# Web Application Firewall (WAF)

WAF = web application firewall 

> Be prepared to evaluate different scenarios that involve needing to block malicious IP addresses

(note that the sister topic is network ACLs for security (discussed in my S3 doc))

Lets you monitor the HTTP and HTTP requests that are forwarded to 
* CloudFront
* An Application Load Balancer
* API Gateway.

WAF lets you control access to your content.

Helps protect your apps from web exploits that could:
* affect app availability
* compromise security
* consume extra resources

## WAF goes down to Layer 7 aware firewall

* Stops SQL injections
* Stops cross-site scripting
* a query string parameter passes variables. The WAF sees down into layer 7 of the string, so they can see a lot more information. 


## You can configure various conditions

* specific IP addresses that can make a request
* what query string parameters need to be passed for the request to be allowed. 

... and then the **Application Load Balancer, CloudFront or API Gateway** will either allow the receipt of the content, or give a HTTP 403 Status Code.

## WAF allows 3 different behaviors

1. Allow all requests except the ones you specify

    This is useful when you want CloudFront or an Application Load Balancer to serve content for a public website, but you also want to block requests from attackers.

2. Block all requests except the ones you specify

    This is useful when you want to serve content for a restricted website whose users are readily identifiable by properties in web requests, such as the IP addresses that they use to browse to the website.

3. Count the requests that match the properties you specify

    When you want to allow or block requests based on new properties in web requests, you first can configure AWS WAF to count the requests that match those properties without allowing or blocking those requests. 
    
    This lets you confirm that you didn't accidentally configure AWS WAF to block all the traffic to your website. When you're confident that you specified the correct properties, you can change the behavior to allow or block requests.

## WAF provide extra protection against web attacks

You can get really specific regarding web requests:

* IP addresses that requests originate from
* Country that requests originate from
* Values in request headers
* Strings in requests - specific or regex matching patterns
* length of requests
* Presence of SQL code that's likely malicious (SQL injection)
* Presence of a script likely to be malicious (cross-site scripting)

## CloudFront

You can configure CloudFront to return a custom error page when a request is blocked.