# Cognito & Web Identity Federation

## Index

* [Cognito](#Cognito)
* [Identity Pools](#Identity-Pools)
* [Manage Identity Providers](#Manage-Identity-Providers)
* [MFA](#MFA)
* [User Pools](#User-Pools)
* [Web Identity Federation](#Web-Identity-Federation)

# Web Identity Federation

Your users are allowed to gain access to AWS resources after they authenticate with a **Web Identity Provider** (Amazon, FB, Google). 

The user receives an authentication code after successful authentication from the **Web ID provider**. They can then trade that code for temporary security credentials.

Ultimately just lets you sign into a website using your FB or Google credentials, etc. 

# Cognito 

> A Web IDentity Federation service

Allows you to sign-in / sign-up to your apps

Allows access for guest users

Cognito acts as an **Identity Broker** between your app and *Web ID providers*, so you don't need to write extra code.

Synchronizes user data from multiple devices

* If you change the username on one device, it will trickle out to your other devices.

Recommended for all mobile applications with AWS services.

Say you have a website with Lambda, DynamoDB, S3... Series of events:

* User will authenticate with FB (for example). 
* FB gives the user an authentication token
* User sends auth token to cognito
* Cognito responds and grants access to the AWS env't (depending on the access yout permit)
* User can start lambda functions, store data in the DB or store images in S3, etc

## Benefits of Cognito

* No need for the app to imbed or store AWS credentials on the device
* Users get a seamless experience across all mobile devices

## User Pools

A user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito. Your users can also sign in through social identity providers like Google, Facebook, Amazon, or Apple, and through SAML identity providers. Whether your users sign in directly or through a third party, all members of the user pool have a directory profile that you can access through a Software Development Kit (SDK).

User pools provide:

* Sign-up and sign-in services.
* A built-in, customizable web UI to sign in users.
* Social sign-in with Facebook, Google, Login with Amazon, and Sign in with Apple, as well as sign-in with SAML identity providers from your user pool.
* User directory management and user profiles.
* Security features such as multi-factor authentication (MFA), checks for compromised credentials, account takeover protection, and phone and email verification.
* Customized workflows and user migration through AWS Lambda triggers.

After successfully authenticating a user, Amazon Cognito issues **JSON web tokens (JWT)** that you can use to secure and authorize access to your own APIs, or exchange for AWS credentials.

### MFA

You can add multi-factor authentication (MFA) to a user pool to protect the identity of your users. MFA adds a second authentication method that doesn't rely solely on user name and password. You can choose to use SMS text messages, or time-based one-time (TOTP) passwords as second factors in signing in your users. 

You can also use adaptive authentication with its risk-based model to predict when you might need another authentication factor. It's part of the user pool advanced security features, which also include protections against compromised credentials.

## Identity Pools

Lets you retrive an Amazon Cognito identity from an identity pool.

Amazon Cognito identity pools enable you to create unique identities for your users and authenticate them with identity providers. With an identity, you can obtain temporary, limited-privilege AWS credentials to access other AWS services. Amazon Cognito identity pools support public identity providers—Amazon, Apple, Facebook, and Google—as well as unauthenticated identities. It also supports developer authenticated identities, which let you register and authenticate users via your own back-end authentication process.

* deliver temporary, limited-privilege credentials to your application so that your users can access AWS resources. 
* You can retrieve a unique Amazon Cognito identifier (identity ID) for your end user immediately if you're allowing unauthenticated users or after you've set the login tokens in the credentials provider if you're authenticating users. 

### Step-by-Step

* User logs in using FB account
* FB authenticates her and passes back an authentication token to Cognito.
* Cognito converts that token to a JSON Web Token (JWT)
* User sends the JWT to an Identity Pool
* Identity Pool grants the user credentials in the form of an IAM role
* User can access AWS resources with an identity ID

### Example

Your mobile app authenticates with the Identity Provider (IdP) using the provider's SDK and Amazon Cognito. Once the end user is authenticated with the IdP, the OAuth or OpenID Connect token returned from the IdP is passed by your app to Amazon Cognito.

Which of the following is returned for the user to provide a set of temporary, limited-privilege AWS credentials? 

--> Cognito ID

## Cognito Synchronisation

Cognito uses Push Synchronization to push updates and synchronize user data across multiple devices.

Uses *SNS* to send a notification to all devices associated with a given user identity whenever data stored in the cloud changes.

## Manage Identity Providers

You can manage identity providers using IAM Dashboard instead of creating IAM users in your AWS account. With an identity provider (IdP), you can manage your user identities outside of AWS and give these external user identities permission to use AWS resources in your account. [See my file on this topic](https://github.com/SharinaS/Cloud-Engineering-Fundamentals/blob/master/authentication.md)

