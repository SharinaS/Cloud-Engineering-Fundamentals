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

A fully-managed state tracker and task coordinator service. 

### Not Serverless

It does not provide serverless orchestration to multiple AWS resources.

## Use Cases

* media processing
* web application backends
* busines process workflows
* analytics pipelines

...  designed as a coordination of tasks

### Use with EC2

For: 

* Managing a multi-step and multi-decision checkout process of an e-commerce mobile app.

* Orchestrating the execution of distributed business processes

## Tasks Invocations

Tasks are invocations of various processing steps in an application, which are preformed by:

* executable code
* web service calls
* human actions
* scripts

A way of combining the digital environment with human beings.

In SWF, you've gotten a human element to factor into the design process. If there's any sort of manual process, you will be using SWF.


## Work Flow Executions

By default, each workflow execution can run for a maximum of 1 year in Amazon SWF. This means that it is possible that in your workflow, there are some tasks which require manual action that renders it idle. As a result, some orders get stuck for, say, 4 weeks.

Amazon SWF does not take any special action if a workflow execution is idle for an extended period of time. Idle executions are subject to the timeouts that you configure. For example, if you have set the maximum duration for an execution to be 1 day, then an idle execution will be timed out if it exceeds the 1 day limit. Idle executions are also subject to the Amazon SWF limit on how long an execution can run (1 year).

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