# CORS 

[CORS](#CORS)

* [How CORS works](#How-CORS-works)

CORS = Cross Origin Resource Sharing

## Same Origin Policy

A concept in the web application security model.

States that *if two web pages have the same origin*, a web browser can permit scripts contained in one web page to access data in a second web page.

* They must have the same domain name

Same-origin policy is done to prevent Cross-Site Scripting (XSS) attacks.

* One web site attacks another website
* Postman and curl ignore Same Origin Policy

Note that in AWS, we are constantly using different domain names - S3 has a different domain name than Cloudfront, which is different from the API Gateway domain name, etc.

## About CORS

A way to bypass Same-Origin Policy - One way the server at the other end (not the client code in the browser) can relax the same-origin policy.

CORS allows restricted resources on Domain A's web page to be requested from Bomain B - outside Domain A.

* ie, fonts on the webpage

Allows one domain to talk to another domain and request resources from that other domain.  

Cors is *enforced by the client's* browser

### JS / AJAX and Multiple Domains

If you're using Javascript/AJAX that uses multiple domains with API Gateway, make sure to enable CORS on API Gateway.

## How CORS works

Browser makes an HTTP OPTIONS call for a URL

* OPTIONS is an HTTP method like GET, PUT, POST, etc

Server returns a response that says:

* "these other domains are approved to GET this URL"

Error message may show up:

* "Origin policy cannot be read at the remote resource"

    ^-- This error message means you *need to enable CORS* on the API Gateway. 
