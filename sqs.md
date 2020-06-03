[SQS](#SQS)

* [About SQS](#About-SQS)
* [Duplication Scenarios](#Duplication-Scenarios)
* [Use Cases](#Examples)
* [Types of Queues](#Types-of-Queues)
* [Visibility Timeout](#Visibility-Timeout)

# SQS

SQS = Simple Queue Service

If you are building brand new applications in the cloud, then it is highly recommended that you consider Amazon SQS and Amazon SNS. Amazon SQS and SNS are lightweight, fully managed message queue and topic services that scale almost infinitely and provide simple, easy-to-use APIs. You can use Amazon SQS and SNS to decouple and scale microservices, distributed systems, and serverless applications, and improve reliability.

Not great for migrating a messaging service:

* does not support an extensive list of industry-standard messaging APIs and protocol, unlike Amazon MQ. Moreover, using Amazon SQS requires you to do additional changes in the messaging code of applications to make it compatible.

## About SQS

The oldest service in AWS

* SQS comes up a lot in the Solutions Architect exam.

> Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to send, store, and receive messages between software components at any volume, without losing messages or requiring other services to be available. SQS lets you decouple application components so that they run independently, increasing the overall fault tolerance of the system. Multiple copies of every message are stored redundantly across multiple availability zones so that they are available whenever needed. 

### SQS is a web service that gives you access to a message queue

* SQS is a queue system - queue can be used to store messages while waiting for a computer to process them
* Lets you store store messages independently from your EC2 instances.

### SQS is a distributed queue system

* The queue is a temporary repository for messages that are awaiting processing. 
* Enables web service applications to quickly queue mesages
  * messages are **generated** by one component in the application and **consumed** by another application component.

See AWS Developer Guide on [Distributed Queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-architecture.html)


### SQS lets you decouple the components of an application so they run independently.

* decouple infrastructure
* decouple microservices
* Any component of a distributed application can store messages in a fail-safe queue.
* Anytime you see the word, *decouple*, think SQS!!!

### The queue acts as a **buffer**.

* buffering occurs between the component producing and saving data, and the component receiving the data for processing. 
* If the producer is creating work faster than the consumer can process it
* The *producer or consumer* are only intermittently connected to the network

### SQS is **pull-based**, versus push-based

* SQS messages must be *pulled* out of the queue by an EC2 instance.
* In contrast, SNS is push-based



## Size

Messages can be *<= 256 KB* of text in any format.

Anything above 256 KB will be stored in S3, versus SQS, up to about 2 gigs in size.

A single Amazon SQS message queue can contain an unlimited number of messages. However, there is a 120,000 limit for the number of inflight messages for a standard queue and 20,000 for a FIFO queue. Messages are inflight after they have been received from the queue by a consuming component, but have not yet been deleted from the queue.

## Time-Frame

Messages can be kept in the queue from 1 minute - 14 days.

*Default* retention period: 4 days.

Once the message retention limit is reached, your messages are automatically deleted.

### Scenario

You developed a web application and deployed it on a fleet of EC2 instances, which is using Amazon SQS. The requests are saved as messages in the SQS queue which is configured with the **maximum message retention period.**  However, after thirteen days of operation, the web application suddenly crashed and there are 10,000 unprocessed messages that are still waiting in the queue. Since you developed the application, you can easily resolve the issue but you need to send a communication to the users on the issue. 

--> Tell the users that the application will be operational shortly and all received requests will be processed after the web application is restarted.

## Visibility Timeout

> Invisibility time for the message that reduces the liklihood of **duplicate messages**.

The amount of time the message is invisible in the SQS queue **after a reader picks up** that message.

If the jobs is processed before the visibility timeout expires, the message will be *deleted* from the queue.

If the job is *not* processed before the visibility timeout expires, the message will *become visible again* and another reader will process it. 

* Could result in the same message being delivered twice.
* Longer visibility timeout means the messages don't become visible once obtained by a consumer

Visibility timeout maximum: 12 hours

* If the job is not completed by 12 hours, the message will become visible in the queue again.

### Scenario

You're getting the same message delivered twice. Why?

The visibility timeout period is not long enough. Increase the length of the timeout period to reduce duplication.


## Types of Queues

* Standard
* FIFO

### Standard

The default type of queue.

Transactions per second: *unlimited*

Guarantee: a message is delivered *at least once*.

* Sometimes more than one copy of a message will be delivered... out of order.
  * due to the highly-distributed architecture that allows high throughput (throughput = rate of production or the rate at which something is processed)
  * Standard queues provide *best-effort ordering*, however, which means that messages are generally delivered in the same order as they are sent.

Application must be able to cope with:

* Messages delivered out of order (occasionally)
* Multiple copies of the same message delivered twice.

Great for applications that can deal with messages occasionally out of order and isn't effected by duplicates.

If you can't at all handle having any duplicates, you can:

- Replace Amazon SQS and instead, use Amazon Simple Workflow service.
- Use an Amazon SQS FIFO Queue instead.

### FIFO

Complements the standard queue.

> Choose FIFO queues when your application can't cope with messages out of sequence or duplicates.

Has *FIFO delivery*: first-in-first-out

* Order in which messages are sent / received is always the same
* Messages are in sequence

Has *exactly-once processing* 

* A message is delivered once and remains available until a consumer processes and deletes it
* Duplicates are never introduced to the queue.

Supports *message groups*

* message groups allow multiple ordered message groups within a single queue.
* Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group (however, messages that belong to different message groups might be processed out of order)

Transactions per second: limited to *300 transactions / second (TPS)*

* **Not as fast** as standard queues.

## Amazon SQS Long Polling

> Reduces the cost of using SQS by eliminating the number of **empty responses**.

A way to retrieve messages from your SQS queues. [See more on AWS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html#sqs-long-polling).

Short polling is the regular way to do it. Short polling *returns immediately*, even if the message queue being polled is empty.

Long polling *doesn't return a response until*:

* a message arrives in the message queue,
* or the long poll times out

Long polling reduces the number of empty resonses by allowing SQS to **wait until a message is available**... before sending a response to a ReceiveMessage request.

### Start Long Polling 

Change the ReceiveMessageWaitTimeSeconds property of a queue to a value greater than zero.

In other words, increase the receive message wait time for the queue.

### Example

You've got a queue that's mostly empty, and you've got EC2 instances polling the queue looking for work.

This costs you money, because for every poll, there's a charge.

Reduce the charges by switching to long polling. It won't return a response until a message enters the queue or until timeout of the poll occurs.

## Examples

### Memes

* User uploads a picture
* S3 receives the picture
* Lambda function is triggered
* Lambda writes some text over the image
* Text is stored in SQS
* A fleet of EC2 instances will:
  * pull the message queue and look for work
  * put the text on the picture stored in S3 to create a meme
  * store the meme on the S3 bucket

SQS serves as a way to store messages independently so that even if an EC2 instance is lost, and can't process the message, another instance will appear and take care of the message being stored in the queue.

### Cheap Fares

Events:

* User goes to an EC2 webserver to search for deal on flights. 
* EC2 instance passes the information to an SQS queue
* Application servers (a fleet) in the background will grab the info in the queue
  * servers will then search for airline deals on various sites. 
* best deals will be collected by the EC2 instances and forwarded to the webserver
* webserver passes the information to the user

If we lose an EC2 instance, another instance will appear, so the search will go on.

## Number of SQS Queues to Set Up

If you have to determine priority for polling to occur from messages, set up the number of queues for the number of priority values.

Problem: You need to ensure that the premium users of a site who paid for the service have higher priority than your free members. 

Solution: Create an SQS queue for free members and anothe one for premium members. Configure your EC2 instances to consume messages from the premium queue first, and if it's empty, poll from the free members' SQS queue. 

The reason for this is that you cannot set a priority to individual items in an SQS queue.

## SQS and SWF

 The services that you can use for creating a decoupled architecture in AWS. Decoupled architecture is a type of computing architecture that enables computing components or layers to execute independently while still interfacing with each other.

Amazon SQS offers reliable, highly-scalable hosted queues for storing messages while they travel between applications or microservices. Amazon SQS lets you move data between distributed application components and helps you decouple these components. 

Amazon SWF is a web service that makes it easy to coordinate work across distributed application components. It ensures that a task is never duplicated and is assigned only once.

## SQS vs SWF

The Thing|SQS|SWF|
|-----|-----|-----|
|Timing|Retention period up to 14 days| workflow executions up to 1 year|
|API|message-oriented|*task*-oriented|
|Duplication|Need to handle duplicated messages (default type)|A task is assigned only once and is never duplicated|
|Tracking|Need to implement your own app-level tracking (esp w/multiple queues)|Keeps track of all tasks and events in an app|

## Duplication Scenarios

After a message is added to an SQS queue, SQS does not automatically delete the messages.

### Scenario

You have built a web application that checks for new items in an S3 bucket once every hour. If new items exist, a message is added to an SQS queue. You have a fleet of EC2 instances which retrieve messages from the SQS queue, process the file, and finally, send you and the user an email confirmation that the item has been successfully processed. Your officemate uploaded one test file to the S3 bucket and after a couple of hours, you noticed that you and your officemate have 50 emails from your application with the same message.

--> In this scenario, the main culprit is that your application does not issue a delete command to the SQS queue after processing the message, which is why this message went back to the queue and was processed multiple times.

### Scenario

You have a web-based order processing system which is currently using a standard queue in Amazon SQS. The support team noticed that there are a lot of cases where an order was processed twice. This issue has caused a lot of trouble in your processing and made your customers very unhappy. Your IT Manager has asked you to ensure that this issue will not recur.

The main issue in this scenario is that the order management system produces duplicate orders at times. Since the company is using SQS, there is a possibility that a message can have a duplicate in case an EC2 instance failed to delete the already processed message. 

To prevent this issue from happening:

- Replace Amazon SQS and instead, use Amazon Simple Workflow service.

- Use an Amazon SQS FIFO Queue instead.