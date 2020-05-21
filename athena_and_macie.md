# Athena

An interactive *query service* of S3.

* Analyze and query data located in S3
* Use standard **SQL**
* Works directly with data stored in S3

Essentially allows you to turn S3 into a database, so you can query the data using SQL.

A managed service.

## Benefits

Serverless (nothing to provision)

No need to set up complex Extract/Transform/Load (ETL) processes

## Cost

Pay per query

Pay per TB scanned

## Used For

* Query log files stored in S3
  * ELB logs
  * S3 access logs
* Generate business reports on data stored in S3
  * Pay-roll data, for example
* Analyze AWS cost and usage reports
* Run queries on click-stream data

# Macie

## PII - Personally Identifiable Information

Personal data used to establish an individual's identity.

Can be exploited by criminals

* identity theft
* financial fraud

## About Macie

Uses Machine Learning and Natural Language Processing (NLP) to deal with sensitive data stored in S3.

Macie is essentially a **security** service.

* Discovers sensitive data
* Classifies the data
* Protects the PII 

Provides:

* Dashboards
* reporting
* alerts

Works directly with data stored in S3

Great for those taking credit card information on their website

* Macie is great for PCI-DSS and preventing ID theft

## Macie and CloudTrail

Macie can analyze CloudTrail logs for suspicious API activity.