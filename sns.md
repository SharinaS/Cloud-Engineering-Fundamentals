# SNS

## Index

[SNS](#SNS)

* [Benefits](#Benefits)
* [SQS vs SNS](#SQS-vs-SNS)

# SNS

## About SNS

A web service that works with notifications from the cloud

* set up, operate, send notifications

Lets developers publish messages from an application, to send them to subscribers or applications

* highly scalable
* flexible
* cost-effective

## Send push notifications

* Directly to mobile devices
  * Apple
  * Google
  * Fire OS
  * Windows devices
  * Android devices in China with Baidu Cloud Push
* By SMS text message or email to Amazon Simple Queue Service (SQS) queues
* HTTP endpoint(s)

SNS lets you group multiple recipients using *topics*

* Allows recipients to dynamically subscribe for identical copies of the same notification.
* When you set a billing alert, that's a topic. When you set an autoscaling alert, that's a separate topic.
* One topic supports deliveries to multiple endpoint types.
  * An endpoint type could be a group of iOS, Android and SMS recipients.

Once you create a topic on SNS, an *Amazon Resource Name* is created.

Once you publish to a topic, SNS delivers nicely formatted copies of your message to each subscriber.

## Storage

All messages published to SNS are stored redundantly across multiple AZs -- prevents messages from being lost.

## Benefits

* Push-based delivery (no polling) that is instant
* Simple APIs with reasonably straightforward integration into applications
* Flexible message delivery over multiple transport protocols
* Inexpensive
  * Pay as you go model with no up-front costs
* Point and click interface on the web-based AWS Management Console

## SQS vs SNS

Category|SQS|SNS|
|-----|-----|-----|
|Service Type|messaging|messaging|
|Push or Pull|pull-based (polls) - instances must poll the queue|push-based - messages are pushed out to different devices.|
