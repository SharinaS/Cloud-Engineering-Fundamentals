# Organizations and RAM

# Organizations

Allows you to have multiple AWS accounts and centrally manage them. 

## Best Practices

Always:

* Enable multi-factor authentication on root account
* Use a strong and complex password on root account

Separate the paying account from other accounts

* Paying account should be used *only* for billing purposes
* Don't deploy resources into the paying account

## Service Control Policies (SCP)

Use to enable or disable AWS services

Use SCP on organizational units (ou) or on individual accounts

SCPs aren't available if your organization has enabled *only* the consolidated billing features

Use to prevent, say, the finance team and all the AWS accounts associated with it able to provision EC2 instances.

> SCPs alone are not sufficient for allowing access in the accounts in your organization. Attaching an SCP to an AWS Organizations entity (root, OU, or account) defines a guardrail for what actions the principals can perform. You still need to attach identity-based or resource-based policies to principals or resources in your organization's accounts to actually grant permissions to them. -- [AWS](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html)

## Consolidated Billing

Consolidated billing is always enabled for organizations. 

All other features are either *enabled as a group* or disabled as a group; you can't individually enable or disable a feature. 

Organization allows for consolidated billing, which is beneficial in that the more you use, the less you pay for a service. 

* One bill per AWS account

# Resource Access Manager

AWS Resource Access Manager (RAM) is a service that enables you to easily and securely share AWS resources with any AWS account or within your AWS Organization. 

You can share 

* AWS Transit Gateways
* Subnets
* AWS License Manager configurations
* Amazon Route 53 Resolver rules resources

Many organizations use multiple accounts to create administrative or billing isolation, and limit the impact of errors. RAM eliminates the need to create duplicate resources in multiple accounts, reducing the operational overhead of managing those resources in every single account you own. 

You can create resources centrally in a multi-account environment, and use RAM to share those resources across accounts in three simple steps: 

1. create a Resource Share,
2. specify resources, and 
3. specify accounts. 

RAM is available to you at no additional charge.

RAM leverages existing policies and permissions set in AWS Identity and Access Management (IAM) to govern the consumption of shared resources. RAM also provides comprehensive visibility into shared resources to set alarms and visualize logs through integration with Amazon CloudWatch and AWS CloudTrail.

* reduce operational overhead
* improve security and visibility
* optimize costs

-- [AWS](https://aws.amazon.com/ram/)
