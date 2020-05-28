# Cognito & Web Identity Federation

## Index

[Cognito](#Cognito)

[Web Identity Federation](#Web-Identity-Federation)

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

### Recommended Approach

Say you have a website with:

* Lambda
* DynamoDB
* S3

Series of events:

* User will authenticate with FB (for example). 
* FB gives the user an authentication token
* User sends auth token to cognito
* Cognito responds and grants access to the AWS env't (depending on the access yout permit)
* User can start lambda functions, store data in the DB or store images in S3, etc

### Benefits of Cognito

* No need for the app to imbed or store AWS credentials on the device
* Users get a seamless experience across all mobile devices

### User Pools

> Takes care of the user side of authentication - usernames, passwords, registration

They are directories that manage sign-up and sign-in functionality for mobile and web apps.

Users can sign-in directly to the User Pool - username/password is stored within Cognito itself.

Or, users can sign-in using FB, Amazon or Google - third party.

* Cognito acts as an *Identity Broker* between the identity provider and AWS. 
* Successful authentication generates a JSON Web token (JWT). 

### Identity Pools

> Takes care of authorizing access to AWS resources - the actual granting of authentication

Provide *temporary* AWS credentials to access AWS services like S3 or DynamoDB.

### Step-by-Step

* User logs in using FB account
* FB authenticates her and passes back an authentication token to Cognito.
* Cognito converts that token to a JSON Web Token (JWT)
* User sends the JWT to an Identity Pool
* Identity Pool grants the user credentials in the form of an IAM role
* User can access AWS resources

### Cognito Synchronisation

Cognito uses Push Synchronization to push updates and synchronize user data across multiple devices.

Uses *SNS* to send a notification to all devices associated with a given user identity whenever data stored in the cloud changes.
