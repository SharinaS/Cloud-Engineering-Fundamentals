# STS

STS = Security Token Service

> Issue short-lived access tokens that acts as temporary security credentials to allow access to your AWS resources

AWS Security Token Service (AWS STS) is the service that you can use to create and provide trusted users with temporary security credentials that can control access to your AWS resources. Temporary security credentials work almost identically to the long-term access key credentials that your IAM users can use.

Found in CloudFormation templates

```cloudformation
Type: AWS::IAM::Role
Properties:
  AssumeRolePolicyDocument:
  ...
    Action: sts:AssumeRole
```

## Example

IAM user Alice in the Dev account (the role-assuming account) needs to access the Prod account (the role-owning account). Here’s how it works:

Alice in the Dev account assumes an IAM role (WriteAccess) in the Prod account by calling AssumeRole.

STS returns a set of temporary security credentials.

Alice uses the temporary security credentials to access services and resources in the Prod account. Alice could, for example, make calls to Amazon S3 and Amazon EC2, which are granted by the WriteAccess role.

## Compare a Few Services

| Cognito JWT | SSO | STS |
| ----- |-----|-----|
| Amazon Cognito service is primarily used for user authentication and not for providing access to your AWS resources. | AWS Single Sign-On (SSO) is a cloud SSO service that makes it easy to centrally manage SSO access to multiple AWS accounts and business applications. | AWS Security Token Service (AWS STS) is the service that you can use to create and provide trusted users with temporary security credentials|
|A JSON Web Token (JWT) is meant to be used for user authentication and session management.| AWS SSO service uses STS, but it does not issue short-lived credentials by itself | Temporarily control access to your AWS resources.|