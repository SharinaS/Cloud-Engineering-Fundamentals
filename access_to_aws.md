# Directory Service

provides multiple ways to use Amazon **Cloud Directory** and Microsoft **Active Directory** (AD) with other AWS services.

Directories store information about users, groups, and devices, and administrators use them to manage access to information and resources. 

AWS Directory Service provides multiple directory choices for customers who want to use existing Microsoft AD or Lightweight Directory Access Protocol (LDAP)–aware applications in the cloud. It also offers those same choices to developers who need a directory to manage users, groups, devices, and access.

## Directory Service AD Connector

If a company is using a corporate Active Directory, it is best to use AWS Directory Service AD Connector for easier integration. 

For example, a company is planning to give console access to developers, and the roles are already assigned using groups in the corporate Active Directory. Use an **AWS Directory Service AD Connector and IAM roles** to give developers access to the console. 
 
Take note that you can assign an IAM Role to the users or groups from your Active Directory once it is integrated with your VPC via the AWS Directory Service AD Connector.

> roles assigned using AD = use AD Connector

## Directory Service Simple AD

provides a subset of the features offered by AWS Managed Microsoft AD, including the ability to manage user accounts and group memberships, create and apply group policies, securely connect to Amazon EC2 instances, and provide Kerberos-based single sign-on (SSO).

## Microsoft Active Directory Federation Service (AD FS)

Goal: A company has resources hosted on both their on-premises network and in AWS cloud. They want all of their Software Architects to access resources on both environments using their on-premises credentials, which is stored in **Active Directory**.

Solution: Since the company is using Microsoft Active Directory which implements **Security Assertion Markup Language (SAML)**, you can set up a **SAML-Based Federation** for API Access to your AWS cloud. In this way, you can easily connect to AWS using the login credentials of your on-premises network. In other words, set up SAML 2.0-Based Federation by using a Microsoft Active Directory Federation Service (AD FS)

> Hybrid environment = use Federation Service
