# Serverless

## Brief History of the Cloud

It all started with data centers. It could take from 10 days to months to provision.

EC2 (Infrastructure as a service) launched in 2006. Using a simple API call would provision machines. This changed the entire architecture. 

RackSpace and Amazon went to war together, but Amazon flourished b/c of its funding.

Platform as a service later launched (example is Elastic Beanstalk). 

In 2014, containers took off - for example Docker. Still have to manage things in the containers.

Serverless evolved after containers (Only paying for the code being executed). All you have to worry about is the code.

# Serverless AWS Services

Services that are serverless:

* Aurora serverless (only RDS that is serverless)
* DynamoDB
* S3
* Lambda
* API Gateway

# Traditional vs Serverless Architecture

## Traditional

Relies on operating systems. Highly Available. Can tolerate failure when designed to.  

* User sends a request
* request hits Route53
* Route 53 sends it to an Elastic Load Balancer
* ELB sends it to webservers
* Webservers send it to Database servers
* Data sent back to the user

Bottle-neck point is with the RDS instances (unless you're using Aurora)

## Serverless

Scaling happens automatically. Considered a much better way to better architect.

* User sends a request over to API Gateway
* API Gateway sends it to Lambda
* Write to DynamoDB or Aurora
* Response back to user

Architecture Components:

* API Gateway to serve requests.
* Serverless DB
  * DynamoDB or
  * Aurora
* Lambda function(s)

## Debugging Serverless Architecture

Architectures can get very complicated. Use AWS **X-ray** to debug.

* Designed to debug serverless applications.
* Can be used for non-serverless, too, when there are performance issues. 


# Build a Serverless WebPage

Step-by-step...

## Create a Lambda function in the AWS Console

AWS Services > Compute > Lambda

Click "Create a function"

Choose "Author from scratch"

Give it a Name 

Choose a Runtime Environment

* Python 3.6

For "Role" - "Create a new role from one or more templates 

Give the role a name.

For Policy templates, think about what you want the Lambda to do. 

* Simple microservice permissions

Click "Create function"

* It can take some time, depending on internet speed.

## Below the Lambda Function is a "Configuration" section

### Function Code

After lambda creation, scroll down below the lambda, to the section titled "Function code."

* This is our IDE

Add python code into the AWS IDE

* Return a 200 response code with a header and a body (my name)
* If something invokes the code, it will respond with my name.

```python
def lambda_handler(event, contect):
    print("In lambda handler")

    resp = {
        "statusCode": 200,
        "headers": {
            "Access-Control-Allow-Origin": "*",
        },
        "body": "Sharina Stubbs"
    }

    return resp
```

*Make sure to hit the big "Save" button on the upper right hand side of the screen after any changes in the IDE"*


### Scroll down to Basic settings

Add a description

Click "Save" upon the very top of the Configuration section

## Configure a Trigger

In the "Designer" on the left, below your Lambda function, click on the trigger you want to configure

* Choose API Gateway for this example

### Below, in the "Configure triggers" section

"API" should say, "Create a new API"

"Security" - choose on "AWS IAM"

Click "Add"

Go up to the top of the page and click "Save"

### Just below the "Designer", you can see an "API Gateway" section.

This is a new API Endpoint

## Open up API Gateway

In the API Gateway section, click on the URL titled something like yourLambdaFunctionName-API

### Change the method

Currently we're allowing any function (in Resources section).

Go up to Actions, inside "Resources" and click "Delete Method"

Go up to Actions, inside "Resources" and click "Create Method"

In the dropdown box that appears under the method name in "Resources," choose "GET"

In the "GET-Setup" side of the page that appears, put a check inside the box next to "Use Lambda Proxy integration."

In the box next to "Lambda Function," start typing the name of your premade function.

Click "Save"

## Deploy the API

Go up to Actions, inside "Resources" and click "Deploy API"

For Deployment stage, choose "default" 

Give it a description, like "MyPRodDep." 

Click "Deploy"

## Check It

In the "Stages" column that should appear, you should see "default." 

Click the arrow next to Default to open up the file structure. Click on "GET"

To the right, click on the "Invoke URL" link which starts with https://.....

You should see a window that appears which shows your name. 

Go back, once you confirm the body of your python code has appeared.

Copy the "Invoke URL" link.

## Set up a Webpage

Paste in API Gateway link down below. 

The Click me button is going to run the function, which will send a GET request to the API Gateway.

```html
<html>
<script>

function myFunction() {
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
        document.getElementById("my-demo").innerHTML = this.responseText;
        }
    };
    xhttp.open("GET", "YOUR-API-GATEWAY-LINK-HERE", true);
    xhttp.send();

}

</script>
<body><div align="center"><br><br><br><br>
<h1>Hello <span id="my-demo">Cloud Gurus!</span></h1>
<button onclick="myFunction()">Click me</button><br>
<img src="https://s3.amazonaws.com/acloudguru-opsworkslab-donotdelete/ACG_Austin.JPG"></div>
</body>
</html>
```

## Configure S3

Can use a Route53 domain (or subdomain) name. 

Create a new bucket. Name it to match your domain name, with the .com at the end. 

```
hello.com
```

### Open the bucket to the world, so you can host a website

Click the radio button next to the bucket.

Above, click "Edit Public Access Settings"

* They should all be unchecked.
* Click "Save"

Your bucket should now say, under "Access," "Objects can be public"

Go into the bucket - click on the URL

Click on the "Properties" tab.

Click on the box that says "Static website hosting"

* Click on "Use this bucket to host a website"
* Type in "index.html" under Index document and "error.html" under Error document (make sure to add a custom one, if you like). 
* Hit "save"

Back in the bucket, click the tab that says "Upload Files"

* Add your index and error files

Make those files public up in the Actions pulldown.

## Hook Up Route 53 to the S3 Bucket

Go to Route 53

Click "Create Record Set"

* On the left, click "Alias"
* For Alias Target, Choose the S3 website endpoint
* Click "Create"

--------
--------

# Alexa Lab

Point a lambda to an S3 bucket and create an Alexa Study Buddy. 

Anytime you're talking to Alexa, you're talking to Lambda. 

See Certified Solutions Architect Course, Chapter 10.3


