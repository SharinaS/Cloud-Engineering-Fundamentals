# Inspector

**Automated security assessment** service for your EC2 instance(s).

Inspector is not used for performance issues.

 an API-driven service that analyzes network configurations in your AWS account and uses an optional agent for visibility into your Amazon EC2 instances.

 You *install it into your instance*

## Inspector Assesses

Assesses applications for exposure, vulnerabilities, and deviations from best practices.

* Looks for common vulnerabilities in the EC2 instance
  * "These patches need to be applied"
* Looks for deviations from security best practices

Amazon Inspector assessments are offered to you as pre-defined rules packages mapped to common security best practices and vulnerability definitions.

## Inspector Reports

After performing an assessment, Amazon Inspector produces a detailed list of security findings **prioritized by level of severity**. These findings can be reviewed directly or as part of detailed assessment reports which are available via the Amazon Inspector console or API.

Helps improve sercurity and compliance of applications deployed on AWS. 



## Pricing

Pricing is based on two dimensions, the number of EC2 instances included in each assessment, and the type(s) of rules package you select. 

An Inspector assessment can have any combination of two rules package types - host assessment rules packages and/or the network reachability rules package. 

### For example

you have 10 Amazon EC2 instances in your assessment target with the Inspector Agent installed on each instance. During the billing period, you run one assessment that includes both host assessment rules packages (example: CVE, CIS, and security best practices) and the network reachability rules package.

In this example, you would be billed for 10 host agent-assessments and 10 network reachability instance-assessments.