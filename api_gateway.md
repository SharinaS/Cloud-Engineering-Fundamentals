# API Gateway

* [About API Gateway](#About)
* [API Caching](#API-Caching)
* [Configure and Deploy](#Configure-and-Deploy)
* [CORS](#CORS)
* [How CORS works](#How-CORS-works)
* [What the API Gateway Can Do](#What-it-Can-Do)


# About

> Comes up a lot in the Solutions Architect exam

A fully managed service that works with APIs

* Developers can:
  * publish
  * maintain
  * monitor
  * secure APIs.
* Acts as a *doorway* into your AWS environment to access from your back-end services (EC2, Lambda, web application(s)):
  * data
  * business logic
  * functionality


It will distribute traffic. 

Typical usage: Lambda functions

> Increase performance of the API Gateway:
> * Enable caching
> * Set throttling limits

## How it Works

* Users make a call to the API Gateway from a mobile or desktop, etc source.
* Depending on the call, traffic is passed to:
  * lambda
  * EC2
  * writing to DynamoDB

## What it Can Do

* Define a RESTful API
  * Exposes HTTPS endpoints
* Connect to services like Lambda & DynamoDB - serverless connection!
* Send each API endpoint to a different target
* Runs efficiently w/low cost
* Scales effortlessly 
  * No need for autoscaling groups
* Track and control usage by API key
* Connect to CloudWatch to log all reqeusts for monitoring
* Maintain multiple version of your API
  * Ie, test and dev version versus production API

## Prevent Attacks

You can throttle API Gateway requests to prevent attacks

## CloudWatch

You can log results to CloudWatch

## Configure and Deploy

### How to Configure an API Gateway 

1. Define an API (container)
2. Define Resouces and nested resources (URL paths)

For each Resource:

* select supported HTTP methods (verbs)
* set security
* choose target 
  * EC2, lambda, DynamoDB, etc
* set request and response transformations

### How to Deploy

Deploy API to a "stage"

* uses API Gateway domain by default
* or, use a custom domain name

API Gateway supports AWS Certificate Manager, so you can get free SSL/TSL certs.

## API Caching

Enable API caching in API Gateway to cache your endpoint's responses.

API Gateway responds to a request by looking up the endpoint response from the cache, instead of making a request to your endpoint.

### Benefit

* reduce the number of calls made to your endpoint
* improve latency of the requests to your API

### TTL

Responses are cached from your endpont when you enable caching for a stage.

Caching occurs for a specified time-to-live (TTL) period in seconds.

### The Flow of It

* User makes a request
* API Gateway receives the request
* API Gateway sends traffic over to a lambda
* Lambda does things and sends data back to the API Gateway

A second user makes a request that is identical to the prior one.

* API Gateway finds the cached response, skips sending stuff over to the lambda, and returns a response to the user.
