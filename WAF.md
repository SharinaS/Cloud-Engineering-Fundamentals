# Web Application Firewall (WAF)
WAF = web application firewall 

> Be prepared to evaluate different scenarios that involve needing to block malicious IP addresses

(note that the sister topic is network ACLs for security)

Lets you monitor the HTTP and HTTP requests that are forwarded to CloudFront, an Application Load Balancer or API Gateway.

Also lets you control access to your content.

At the Layer 7 aware firewall. 

Remember, a query string parameter passes variables. The WAF sees down into layer 7 of the string, so they can see a lot more information. 

You can configure various conditions:
* specific IP addresses that can make a request
* what query string parameters need to be passed for the request to be allowed. 

... and then the app load balancer, CloudFront or API Gateway will either allow the receipt of the content, or give a HTTP 403 Status Code.

WAF allows 3 different behaviors:
1. Allow all requests except the ones you specify
2. Block all requests except the ones you specify
3. Count the requests that match the properties you specify

Provides extra protection against web attacks. You can get really specific regarding web requests:
* IP addresses that requets originate from
* Country that requests originate from
* Values in request headers
* Strings in requests - specific or regex matching patterns
* length of requests
* Presence of SQL code that's likely malicious (SQL injection)
* Presence of a script likely to be malicious (cross-site scripting)