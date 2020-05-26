# Macie

## About PII 

PII = Personally Identifiable Information

Personal data used to establish an individual's identity.

Can be exploited by criminals

* identity theft
* financial fraud

## About Macie

Uses Machine Learning and Natural Language Processing (NLP) to deal with sensitive data stored in S3.  

Macie helps you meet regulations, such as the Health Insurance Portability and Accountability Act (HIPAA) and General Data Privacy Regulation (GDPR). 

* uses machine learning and pattern matching
* Amazon Macie automates the discovery of sensitive data at scale and lowers the cost of protecting your data.

Macie discovers sensitive data

* Detects data
* Classifies the data
* Protects the PII 
* The service also allows you to define your own custom sensitive data types so you can discover and protect the sensitive data that may be unique to your business or use case.

Provides:

* Dashboards
* reporting
* alerts

Works directly with data stored in S3

Great for those taking credit card information on their website

* Macie is great for PCI-DSS and preventing ID theft

## What Macie Does

Macie automatically provides an inventory of Amazon S3 buckets including a list of unencrypted buckets, publicly accessible buckets, and buckets shared with AWS accounts outside those you have defined in AWS Organizations. 

Then, Macie applies machine learning and pattern matching techniques to the buckets you select to identify and alert you to sensitive data, such as personally identifiable information (PII). Macie’s alerts, or findings, can be searched and filtered in the AWS Management Console and sent to Amazon CloudWatch Events for easy integration with existing workflow or event management systems, or to be used in combination with AWS services, such as AWS Step Functions to take automated remediation actions.

## Use Cases

### Use Macie to sift through DB data

you can initiate RDS or Amazon Aurora snapshots to export data in these services to Amazon S3 where it can be evaluated for sensitive data using Macie. This allows you to utilize Macie to help you maintain data privacy and security.

### Use Macie to check for data when migrating into AWS

When migrating large volumes of data to AWS, you can set up a secure Amazon S3 environment to use as an initial staging area where you use Macie to discover sensitive data. Using Macie’s findings, you can automate the configuration of data protection and role-based access policies as your data moves into AWS.