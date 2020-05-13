# Organizations

## Best Practices

Always:

* Enable multi-factor authentication on root account
* Use a strong and complex password on root account

Separate the paying account from other accounts

* Paying account should be used *only* for billing purposes
* Don't deploy resources into the paying account

Service Control Policies (SCP)

* Use to enable or disable AWS services
* Use SCP on organizational units (ou) or on individual accounts
* Use to prevent, say, the finance team and all the AWS accounts associated with it able to provision EC2 instances. 