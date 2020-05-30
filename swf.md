# SWF

* [About SWF](#About-SWF)
* [SWF and SQS](#SWF-and-SQS)
* [SWF vs SQSF](#SQS-vs-SWF)

------------

SWF = Simple Work Flow

> A fully-managed state tracker and task coordinator service

Think of SWF if a question refers to human workers and a need to manage that work.

## About SWF

A way of coordinating work across distributed application components... or a way to coordinate work across multiple components of your application.

SWF is a way of coordinating both your applications as well with human beings / manual processes

Amazon SWF is a web service that makes it easy to coordinate work across distributed application components.

### Use Cases

Task coordination use cases:

* media processing
* web application backends
* busines process workflows
* analysis piopeslines

### Tasks Invocations

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

## SWF and SQS

 The services that you can use for creating a decoupled architecture in AWS. Decoupled architecture is a type of computing architecture that enables computing components or layers to execute independently while still interfacing with each other.

Amazon SQS offers reliable, highly-scalable hosted queues for storing messages while they travel between applications or microservices. Amazon SQS lets you move data between distributed application components and helps you decouple these components. 

Amazon SWF is a web service that makes it easy to coordinate work across distributed application components.

## SWF vs SQS

The Thing|SQS|SWF|
|-----|-----|-----|
|Timing|Retention period up to 14 days| workflow executions up to 1 year|
|API|message-oriented|*task*-oriented|
|Duplication|Need to handle duplicated messages (default type)|A task is assigned only once and is never duplicated|
|Tracking|Need to implement your own app-level tracking (esp w/multiple queues)|Keeps track of all tasks and events in an app|