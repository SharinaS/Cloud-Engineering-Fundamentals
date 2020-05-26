# GuardDuty

Monitors the security of an account's AWS environment.

A threat detection service that **continuously monitors** for malicious activity and unauthorized behavior to protect your AWS accounts and workloads.

The service uses machine learning, anomaly detection, and integrated threat intelligence to identify and prioritize potential threats.

## Analyzes:

* VPC Flow Logs
* CloudTrail logs 
* DNS query logs

## Review of findings:

* Occurs in the console
* You can integrate into event management or workflow systems
* Can have a lambda triggered to automatically remediate or prevent Bad Things.

## Across Accounts

GuardDuty makes enablement and management across multiple accounts easy. Through the multi-account feature, all member accounts findings can be aggregated with a GuardDuty administrator account.

## CloudWatch

The aggregated findings are also available through CloudWatch Events, making it easy to integrate with an existing enterprise event management system.

## Use Cases

Threat intelligence coupled with machine learning and behavior models help you detect activity such as crypto-currency mining, credential compromise behavior, communication with known command-and-control servers, or API calls from known malicious IPs.