# ACM

ACM = AWS Certificate Manager 

ACM handles the complexity of creating and managing public SSL/TLS certificates for your AWS based websites and applications. You can use public certificates provided by ACM (ACM certificates) or certificates that you import into ACM. ACM certificates can secure multiple domain names and multiple names within a domain. You can also use ACM to create wildcard SSL certificates that can protect an unlimited number of subdomains.

ACM is tightly linked with AWS Certificate Manager Private Certificate Authority. You can use ACM PCA to create a private certificate authority (CA) and then use ACM to issue private certificates.

You can also use ACM to export a private certificate and encrypted private key to use anywhere.

## Use Case

You need to deploy an SSL server certificate - think IAM and ACM

To enable HTTPS connections to your website or application in AWS, you need an SSL/TLS server certificate. You can use a server certificate provided by AWS Certificate Manager (ACM) or one that you obtained from an external provider. You can use ACM or IAM to store and deploy server certificates. Use IAM as a certificate manager only when you must support HTTPS connections in a region that is not supported by ACM. IAM supports deploying server certificates in all regions, but you must obtain your certificate from an external provider for use with AWS.

Note that Amazon Route 53 is used to register domain names or use your own domain name to route your end users to Internet applications. Route 53 is not responsible for creating SSL certifications.