# Sample Lambda Function with SES integration

## Introduction
This simple code is a deployable example of a lambda/ses integration. Although it can run in produciton without a hitch, I'd still create unit tests if I were you.

##Who would want to use this?
If you decided to host a static website on S3 but still want people to send you emails through your contact page, this might be a really nice solution for you. The combination of S3, Lambda, & SES covers the basis for a lot of website owners. 

## Setup
You'll need to setup an api gateway, lambda security role, lambda function, and ses

###Get this repo and build it with maven
```
mvn package
```
Use the packaged jar file in the 'create a lambda function' step

###Create an API Gateway which forwards the following json to the lambda function
```
{ 
    
    "from" : "budilov@gmail.com",
    "subject" : "Hello World",
    "message" : "First Test"
}
```

###Create a lambda role
```
arn:aws:iam::1111111111:role/awsLambdaExecution
```

###Create a lambda function
You can run the below command to create the function, just make sure you're ok with the parameters. Pricing depends on the memory-size, so you might want to choose a smaller footprint there

```
aws lambda create-function \
--region us-east-1 \
--function-name lambda-ses-email \
--zip-file fileb://lambda-ses-1.0-SNAPSHOT.jar \
--role arn:aws:iam::1111111111:role/awsLambdaExecution  \
--handler com.budilov.lambda.ses.Main::sendMail \
--runtime java8 \
--timeout 15 \
--memory-size 512 \
--profile work
```

###Setup SES
In order for this to work, you'll need to authorize the receivers and the senders. Meaning, if all of your receivers and senders are coming from the same domain, you can just authorize/verify the domain name. If not, then you can just authorize/verify the sender and the receiver

###Setup API Gateway
Create the resource and the method for and /email endpoint. I used POST as the method
At the end of the day, doing something like
curl -H "Content-Type: application/json" -X POST -d '{"fromEmail" : "budilov@gmail.com", "subject" : "Hello World", "message" : "First Test"}' https://someassigneddomainname.us-east-1.amazonaws.com/prod/email
