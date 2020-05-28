# AWS Applications Index

[Elastic Transcoder](#Elastic-Transcoder)



[SES](#SES)


[SWF](#SWF)

* [About SWF](#About-SWF)
* [SQS vs SWF](#SQS-vs-SWF)

-------
-------
# SES

SES = Simple Email Service

Amazon SES (Amazon Simple Email Service) is a flexible, affordable, and highly-scalable email messaging platform for businesses and developers.

# SWF

SWF = Simple Work Flow

> A fully-managed state tracker and task coordinator service

Think of SWF if a question refers to human workers and a need to manage that work.

## About SWF

A way of coordinating work across distributed application components... or a way to coordinate work across multiple components of your application.

SWF is a way of coordinating both your applications as well with human beings / manual processes

Task coordination use cases:

* media processing
* web application backends
* busines process workflows
* analysis piopeslines

Tasks are invocations of various processing steps in an application, which are preformed by:

* executable code
* web service calls
* human actions
* scripts

A way of combining the digital environment with human beings.

In SWF, you've gotten a human element to factor into the design process. If there's any sort of manual process, you will be using SWF.

## SWF Actors

### Workflow Starters

An app that can initiate a workflow.

Ie, 

* an e-commere website following the placement of an order
* mobile app searching for bus times

### Deciders

This controls the flow of activity tasks in a workflow execution.

If something has finished / failed in a workflow, a Decider chooses what to do next.

### Activity Workers

These carry out the activity tasks

### SWF Domain

A collection of related workflows.

## SQS vs SWF

The Thing|SQS|SWF|
|-----|-----|-----|
|Timing|Retention period up to 14 days| workflow executions up to 1 year|
|API|message-oriented|*task*-oriented|
|Duplication|Need to handle duplicated messages (default type)|A task is assigned only once and is never duplicated|
|Tracking|Need to implement your own app-level tracking (esp w/multiple queues)|Keeps track of all tasks and events in an app|


# Elastic Transcoder

A media transcoder in the cloud

A way to *convert media files* from their original source format into different formats that will play on smartphones, tablets, PCs, etc.

A way of transcoding video.

Has *presets for popular output formats*, meaning you don't need to guess about which settings work best on particular devices.

## Fees

Pay based on

* the minutes you transcode
* The resolution at which you transcode

## Example

* Upload your video to S3
* A lambda function is triggered
* Lambda function looks at all the metadata and sends the video over to Elastic transcoder
* Transcoder looks at the video and transcodes it to look nice on a wide variety of devices.
* Transcoder ships the transcoded videos to another S3 bucket



