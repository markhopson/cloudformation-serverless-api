# cloudformation-serverless-api

A simple, RESTful API using AWS Serverless (Cloudformation, API Gateway, Lambda, and DynamoDB).

An introduction to the technologies
* Cloudformation
* API Gateway
* Lambda
* DynamoDB

# Overview

This project creates (and deploys) a Restful API with API Gateway, Lambda (Node), and Dynamo using Cloudformation.

Use this project as a guide to learn about the AWS command line tool, and the stack creation and code deploy process for a serverless architecture on AWS.

# Useful Things to Know

* ?

# File Directory

* hello.js
  * Javascript for your Lambda to run
* stack.yml
  * Definitions for your AWS resources (including Roles and Permissions, and Swagger)
  * This is a big file, but don't let it intimidate you
* stack-params.json
  * Parameters for the stack
* node_modules, package.json, and npm-shrinkwrap.json
  * Typical Node stuff (this project requires the aws-sdk module)

# Project Requirements

* Install Node and npm
  * <https://nodejs.org/en>
* Install AWS Command Line Interface (aws-cli)
  * <http://docs.aws.amazon.com/cli/latest/userguide/installing.html>
* Setup an AWS Account (ask the Infrastructure team)
  * Login to AWS Console
  * Generate an `ACCESS_KEY`, `SECRET_ACCESS_KEY` and update `~/.aws/credentials`
    * Or alternatively, configure a second profile (the proper way)
      * <http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html>

# Getting Started

__Check if aws-cli is working__

* The command below lists Lambdas with aws-cli
  * Check if this list matches your Lambdas in your AWS account

```sh
aws lambda list-functions
# {
#     "Functions": [
#         {
#             "Version": "XXX", 
#             "CodeSha256": "XXX", 
#             "FunctionName": "XXX", 
#             "MemorySize": 128, 
#             "CodeSize": 1234, 
#             "FunctionArn": "arn:aws:lambda:us-east-1:1234:function:XXX", 
#             "Handler": "lib/src/hello.handleHttpRequest", 
#             "Role": "arn:aws:iam::1234:role/XXX-Role-ABCD1234", 
#             "Timeout": 3, 
#             "LastModified": "2018-01-01T00:00:00.001+0000", 
#             "Runtime": "nodejs4.3", 
#             "Description": ""
#         }, 
#         ...
```

__Create your stack__

```
aws cloudformation create-stack --stack-name <STACK_NAME> --template-body file://stack.yaml --parameters file://stack-params.json --capabilities CAPABILITY_IAM
```

* Key params:
  * <STACK_NAME>
    * This is the _namespace_ for your stack.  AWS resources created by your stack will have this prepended to their name
  * file://stack.yaml
    * This is the stack file that Cloudformation will use decide what AWS resources to create
  * file://stack-params.json
    * This file contains values for the parameters used in `stack.yml`
* When changes are made to `stack.yml`, use `update-stack` instead of `create-stack`

__Upload your code__

```
aws lambda update-function-code --function-name <FUNCTION_NAME> --zip-file fileb://<FILE.ZIP>
```

1. Zip your code with `hello.js` at the root, including `node_modules/`.
2. For OSX, go to Finder, highlight all files and folders, right-click, and compress
3. Run command below.  Key params:
    * <FUNCTION_NAME>
      * Use `aws lambda list-functions` to find the name of the function created by your stack
    * <FILE.ZIP>
      * The compressed file with `hello.js` and `node_modules/`

__Deploy API Gateway__

1. Go to your API Gateway instance (remember: it will contain your stack name)
    * <https://console.aws.amazon.com/apigateway/home?region=us-east-1#/apis>
2. Go to `Resources` > `Actions` > `Deploy`
3. For `Deployment Stage`, select "prod"

__Try the API__

1. Go to your API Gateway instance > `Stages` > your Deployment Stage (should be "prod")
2. Copy the `Invoke URL` (this is the base URL for your API)
3. Try using your API (run the commands below in Terminal)

```sh
# Send a GET; <USER_ID> can be any String, and POST will use it as the Primary Key for Dynamo
curl -X GET \
  <INVOKE_URL>/users/<USER_ID>/hello

# Send a POST
curl -X POST \
  <INVOKE_URL>/users/<USER_ID>/hello \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"email": "dev@flyerflo.com"
}'
```

