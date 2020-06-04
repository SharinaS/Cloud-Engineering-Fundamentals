# API Gateway

* [About API Gateway](#About)
* [API Caching](#API-Caching)
* [API Types](#API-Types)
* [Billing](#Billing)
* [Configure and Deploy](#Configure-and-Deploy)
* [Performance Considerations](#Performance-Considerations)
* [Security](#Security)
* [Use Cases](#Use-Cases)
* [What the API Gateway Can Do](#What-it-Can-Do)
* [X-Ray](#Trace-and-Analyze-User-Requests)

[AWS Developer Guide here](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html).

## About

> Amazon API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor, and secure APIs at any scale. 

With a few clicks in the AWS Management Console, you can create an API that acts as a “front door” for applications to access data, business logic, or functionality from your back-end services, such as workloads running on Amazon Elastic Compute Cloud (Amazon EC2), code running on AWS Lambda, or any web application. 

Since it can use AWS Lambda, you can run your APIs without servers (which would probably be cheaper and more scaleable).

Acts as a *doorway* into your AWS environment, into your back-end services (EC2, Lambda, web apps), to access:
  * data
  * business logic
  * functionality


It will distribute traffic. 

Typical usage: API Gateway + Lambda functions

## Services API Gateway Works Well With

* EC2
* Lambda
* Any web app

## How it Works

* Users make a call to the API Gateway from a mobile or desktop, etc source.
* Depending on the call, traffic is passed to:
  * Lambda
  * EC2
  * DynamoDB
  * Kinesis
  * etc

## API Types

* RESTful APIs
* Websocket APIs

### RESTful APIs

Build RESTful APIs optimized for serverless workloads and HTTP backends using HTTP APIs. HTTP APIs are the best choice for building APIs that only require API proxy functionality. If your APIs require API proxy functionality and API management features in a single solution, API Gateway also offers REST APIs.

### Websocket APIs

Build real-time two-way communication applications, such as chat apps and streaming dashboards, with WebSocket APIs. API Gateway maintains a persistent connection to handle message transfer between your backend service and your clients.

## Use Cases

> Amazon API Gateway handles all the tasks involved in accepting and processing up to hundreds of thousands of concurrent API calls, including traffic management, authorization and access control, monitoring, and API version management. 

Build RESTful APIs

* Exposes HTTPS endpoints

Enables you to build RESTful APIs and WebSocket APIs that are optimized for serverless workloads

Connect to services like Lambda & DynamoDB - serverless connection!

Traffic control: Send each API endpoint to a different target

Lets you bypass needing autoscaling groups - it scales effortlessly 

Track and control usage by API key

Maintain multiple version of your API

* Ie, test and dev version versus production API

## Billing

Runs efficiently w/low cost.

Amazon API Gateway has no minimum fees or startup costs. You pay only for the API calls you receive and the amount of data transferred out.

## Security

You can throttle API Gateway requests to prevent attacks

API Gateway supports AWS Certificate Manager, so you can get free SSL/TSL certs

## CloudWatch

You can log results to CloudWatch 

## Performance Considerations

To Increase performance of the API Gateway:

> * Enable caching
> * Set throttling limits

## Configure and Deploy

### How to Configure an API Gateway 

1. Define an API (container)
2. Define resources and nested resources (URL paths)

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

>API Gateway supports AWS Certificate Manager, so you can get free SSL/TSL certs.

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

## Trace and Analyze User Requests

### Can't Use CloudTrail or VPC Flow Logs
CloudTrail does not have the capability to trace and analyze user requests as they travel through your Amazon API Gateway APIs. CloudTrail is primarily used for IT audits and API logging of all of your AWS resources.

VPC Flow Logs won't work well because this is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your entire VPC... although it can capture some details about the incoming user requests.

### Use X-Ray

Use AWS X-Ray to trace and analyze user requests as they travel through your Amazon API Gateway APIs to the underlying services. 

API Gateway supports AWS X-Ray tracing for all API Gateway endpoint types: regional, edge-optimized, and private. You can use AWS X-Ray with Amazon API Gateway in all regions where X-Ray is available.

### View an Entire Request

X-Ray gives you an end-to-end view of an entire request, so you can analyze latencies in your APIs and their backend services. 

You can use an X-Ray service map to view the latency of an entire request and that of the downstream services that are integrated with X-Ray. 

And you can configure sampling rules to tell X-Ray which requests to record, at what sampling rates, according to criteria that you specify. 

If you call an API Gateway API from a service that's already being traced, API Gateway passes the trace through, even if X-Ray tracing is not enabled on the API.

### How to Enable

You can enable X-Ray for an API stage by using the API Gateway management console, or by using the API Gateway API or CLI.


