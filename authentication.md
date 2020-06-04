# Authentication and Directory Services

## Index

* [AD Connector](#AD-Connector)
* [AWS User Guide on Providing Access to Externally Authenticated Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_federated-users.html)
* [Custom Identity Broker Application](#Custom-Identity-Broker-Application)
* [Directory Service](#Directory-Service)
* [Simple AD](#Simple-AD)
* [Federation Service](#AD-FS)
* [SAML](#SAML)

## Directory Service

Provides multiple ways to use Amazon **Cloud Directory** and Microsoft **Active Directory** (AD) with other AWS services.

Microsoft Active Directory is a collection of services that help you manage users and devices on a network. 

Microsoft Active Directory is used by Windows applications **to manage access and enable single sign-on (SSO).**

Directories store information about users, groups, and devices, and administrators use them to manage access to information and resources. 

AWS Directory Service provides multiple directory choices for customers who want to use **existing Microsoft AD or Lightweight Directory Access Protocol (LDAP)**–aware applications in the cloud. 

It also offers those same choices to developers who need a directory to manage users, groups, devices, and access.

## AD Connector

If a company is using a corporate Active Directory, it is best to use AWS Directory Service AD Connector for easier integration. 

### Scenario

A company is planning to give console access to developers, and the roles are already assigned using groups in the corporate Active Directory. 

--> Use an **AWS Directory Service AD Connector and IAM roles** to give developers access to the console. 
 
This is because you can assign an IAM Role to the users or groups from your Active Directory once it is integrated with your VPC via the AWS Directory Service AD Connector.

## Simple AD

Simple Active Directory.

Simple AD is a standalone managed directory that is powered by a Samba 4 Active Directory Compatible Server.

Directory Service Simple AD provides a subset of the features offered by AWS Managed Microsoft AD, including the ability to manage user accounts and group memberships, create and apply group policies, securely connect to Amazon EC2 instances, and provide Kerberos-based single sign-on (SSO).

However, note that Simple AD does not support features such as trust relationships with other domains, Active Directory Administrative Center, PowerShell support, Active Directory recycle bin, group managed service accounts, and schema extensions for POSIX and Microsoft applications.

## SAML

Security Assertion Markup Language 2.0 (SAML) is an open standard for exchanging identity and security information with applications and service providers.

Applications and service providers that support SAML enable you to **sign in using your corporate directory credentials**, such as your user name and password from Microsoft Active Directory. 

With SAML, you can use single sign-on (SSO) to sign in to all of your SAML-enabled applications by using a single set of credentials.

By enabling SAML authentication, you also can manage access to your applications centrally. SAML-enabled applications delegate authentication requests to your corporate directory. When users are removed from your directory, they are no longer able to sign in.

You can enable SAML authentication for your AWS accounts by using AWS Identity and Access Management (IAM). 

You can add SAML support to your web and mobile apps running on the AWS Cloud by using Amazon Cognito.

## AD FS

AD FS = Microsoft Active Directory Federation Service 

If your organization already uses an identity provider software package that supports SAML 2.0 (Security Assertion Markup Language 2.0), you can create trust between your organization as an identity provider (IdP) and AWS as the service provider. 

You can then use SAML to provide your users with federated single-sign on (SSO) to the AWS Management Console or federated access to call AWS API operations. 

For example, if your company uses Microsoft Active Directory and Active Directory Federation Services, then you can federate using SAML 2.0.

### Scenario

Goal: A company has resources hosted on both their on-premises network and in AWS cloud. They want all of their Software Architects to access resources on both environments using their on-premises credentials, which is stored in **Active Directory**.

Solution: Since the company is using Microsoft Active Directory which implements **Security Assertion Markup Language (SAML)**, you can set up a **SAML-Based Federation** for API Access to your AWS cloud. In this way, you can easily connect to AWS using the login credentials of your on-premises network. 

In other words, set up SAML 2.0-Based Federation by using a Microsoft Active Directory Federation Service (AD FS)

> Hybrid environment = use Federation Service

## Custom Identity Broker Application

LDAP = Lightweight Directory Access Protocol

If your identity store is not compatible with SAML 2.0, then you can build a custom identity broker application to perform a similar function. The broker application authenticates users, requests temporary credentials for users from AWS, and then provides them to the user to access AWS resources.

The application verifies that employees are signed into the existing corporate network's identity and authentication system, which might use LDAP, Active Directory, or another system. The identity broker application then obtains **temporary security credentials for the employees**.

To get temporary security credentials, the identity broker application calls either AssumeRole or GetFederationToken to obtain temporary security credentials, depending on how you want to manage the policies for users and when the temporary credentials should expire. The call returns temporary security credentials consisting of an AWS access key ID, a secret access key, and a session token. 

The identity broker application makes these temporary security credentials available to the internal company application. The app can then use the temporary credentials to make calls to AWS directly. The app caches the credentials until they expire, and then requests a new set of temporary credentials.

[See the details on AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_federated-users.html).

### Scenario

You have a requirement to integrate the Lightweight Directory Access Protocol (LDAP) directory service of your on-premises data center to your AWS VPC using IAM. The identity store which is currently being used is not compatible with SAML. 

--> Develop an on-premises custom identity broker application and use STS to issue short-lived AWS credentials.

This question is asking you to integrate your LDAP service to IAM. You need to use SAML, STS (Security Token Service) or a custom identity broker.