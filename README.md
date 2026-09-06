# Day 13 — AWS Lambda ⭐⭐⭐⭐

## AWS Lambda

AWS Lambda is a **serverless compute service** provided by Amazon Web Services (AWS).

It allows us to run code without managing the underlying servers.

With Lambda, we write our code, configure the function, and AWS manages the infrastructure required to execute that code.

---

# 1. What is AWS Lambda?

AWS Lambda is a serverless compute service that runs code in response to events.

Instead of creating and managing an EC2 server, we can create a Lambda function and let AWS execute our code when it is needed.

For example:

```python
def lambda_handler(event, context):
    print("Hello from AWS Lambda")

    return "Hello World"
```

AWS Lambda executes this code when the function is invoked.

---

# 2. What is Serverless?

**Serverless does not mean that there are no servers.**

Servers are still used by AWS, but AWS manages the underlying infrastructure for us.

With EC2, we are responsible for managing the server.

With Lambda, AWS manages the infrastructure.

---

## Traditional EC2 Architecture

```text
User
 ↓
EC2
 ↓
Application
```

We may need to manage:

* Server
* Operating system
* Patching
* Software
* Capacity
* Scaling
* Infrastructure

---

## Serverless Lambda Architecture

```text
User / Event
      ↓
API Gateway / AWS Service
      ↓
Lambda
      ↓
Your Code
```

AWS manages the underlying infrastructure.

We primarily manage:

* Code
* Configuration
* Permissions
* Dependencies

---

# 3. EC2 vs Lambda

| EC2                                 | Lambda                                    |
| ----------------------------------- | ----------------------------------------- |
| We manage the server                | AWS manages the underlying infrastructure |
| We choose and manage the OS         | AWS manages the execution environment     |
| Server can run continuously         | Function runs when invoked                |
| We manage scaling                   | Lambda can automatically scale            |
| More infrastructure control         | Less infrastructure control               |
| Suitable for long-running workloads | Suitable for event-driven workloads       |
| We manage server maintenance        | AWS manages server maintenance            |

Simple way to remember:

```text
EC2
=
Manage Servers

Lambda
=
Run Code
```

---

# 4. Lambda Function

A **Lambda function** is the unit of code that AWS Lambda executes.

Example:

```python
def lambda_handler(event, context):
    return "Hello World"
```

A Lambda function contains configuration and code such as:

```text
Lambda Function
     |
     +---- Code
     |
     +---- Runtime
     |
     +---- Handler
     |
     +---- Event
     |
     +---- Execution Role
     |
     +---- Environment Variables
     |
     +---- Timeout
     |
     +---- Memory
     |
     +---- Layers
```

---

# 5. Lambda Runtime

A **runtime** provides the environment required to execute Lambda code.

Lambda supports runtimes for programming languages such as:

* Python
* Node.js
* Java
* .NET
* Ruby
* Custom runtimes

For example:

```text
Python Lambda
      ↓
Python Runtime
      ↓
Python Code
```

Another example:

```text
Node.js Lambda
      ↓
Node.js Runtime
      ↓
JavaScript Code
```

---

# 6. Why Runtime is Important

The runtime determines the environment in which the Lambda code executes.

It is associated with:

* Programming language
* Language version
* Execution environment
* Runtime libraries

For example, if we write Python code, we need a supported Python runtime.

---

# 7. Lambda Handler

The **handler** tells AWS Lambda which function to execute when the Lambda function is invoked.

Suppose we have:

```text
lambda_function.py
```

Inside the file:

```python
def lambda_handler(event, context):
    return "Hello"
```

The handler is:

```text
lambda_function.lambda_handler
```

The general format is:

```text
filename.function_name
```

Example:

```text
app.hello
```

means:

```text
app
 ↓
Python file

hello
 ↓
Function inside the file
```

---

# 8. Handler Example

Suppose the file is:

```text
app.py
```

Inside the file:

```python
def hello(event, context):
    return "Hello AWS"
```

The Lambda handler should be:

```text
app.hello
```

Remember:

```text
File name
    .
Function name
```

---

# 9. Lambda Event

The **event** contains information about what triggered the Lambda function and the input data associated with that invocation.

Example:

```python
def lambda_handler(event, context):
    print(event)

    return "Success"
```

The event structure depends on what invokes the Lambda function.

Lambda can be triggered by services such as:

* API Gateway
* Amazon S3
* Amazon EventBridge
* Amazon SQS
* Amazon SNS
* Scheduled events
* Other AWS services
* AWS SDK or API calls

---

# 10. Example API Gateway Event

If API Gateway invokes Lambda, the event can contain information about the HTTP request.

Depending on the API Gateway configuration, it may contain:

* HTTP method
* Path
* Headers
* Query parameters
* Path parameters
* Request body

Example:

```json
{
    "httpMethod": "GET",
    "path": "/users"
}
```

Lambda can read the event:

```python
def lambda_handler(event, context):

    method = event.get("httpMethod")

    print(method)

    return {
        "statusCode": 200,
        "body": "Request received"
    }
```

---

# 11. S3 Event

Lambda can also be triggered when an object is uploaded to an S3 bucket.

Architecture:

```text
User
 ↓
Upload File
 ↓
S3
 ↓
S3 Event
 ↓
Lambda
 ↓
Process File
```

The event can contain information about:

* Bucket
* Object key
* Event type
* Object information

Lambda receives this information through the:

```python
event
```

parameter.

---

# 12. Lambda Context

The second parameter commonly passed to the Lambda handler is:

```python
context
```

Example:

```python
def lambda_handler(event, context):
    print(context)

    return "Hello"
```

The context object provides information about the current Lambda execution.

It can provide information such as:

* Function name
* Function version
* Request ID
* Remaining execution time

Example:

```python
def lambda_handler(event, context):

    print(context.function_name)

    return "Hello"
```

Simple memory trick:

```text
event
=
Input / Trigger Information

context
=
Execution Information
```

---

# 13. Lambda Execution Role

A Lambda function often needs permission to access other AWS services.

For example:

```text
Lambda
   ↓
Read from S3
```

Lambda needs permission to read from S3.

This is provided through an **IAM execution role**.

The execution role defines what AWS resources and actions the Lambda function can access.

---

# 14. Execution Role Architecture

```text
Lambda Function
      |
      ↓
Execution Role
      |
      ↓
IAM Permissions
      |
      +---- S3
      +---- DynamoDB
      +---- CloudWatch Logs
```

For example, if Lambda needs to read an S3 object, the role needs appropriate permission such as:

```text
s3:GetObject
```

subject to the applicable resource policy and permissions.

---

# 15. Why Lambda Needs an Execution Role

Suppose our Lambda code wants to access S3:

```python
import boto3

s3 = boto3.client("s3")
```

Lambda needs AWS permissions to perform S3 operations.

Without appropriate permissions:

```text
Lambda
 ↓
Try to access S3
 ↓
AWS Authorization
 ↓
Access Denied
```

With appropriate permissions:

```text
Lambda
 ↓
Execution Role
 ↓
IAM Permissions
 ↓
S3
 ↓
Access Allowed
```

---

# 16. Principle of Least Privilege

A very important AWS security principle is:

> Give a Lambda function only the permissions it actually needs.

For example, if a Lambda function only needs to read objects from one S3 bucket, we should not automatically give it broad permissions such as:

```text
s3:*
```

when more restrictive permissions are sufficient.

This is called the:

**Principle of Least Privilege**

---

# 17. Environment Variables

Environment variables allow us to store configuration values separately from the application code.

Examples:

```text
DATABASE_HOST
API_URL
ENVIRONMENT
BUCKET_NAME
```

Python can read environment variables using:

```python
import os

def lambda_handler(event, context):

    bucket = os.environ.get("BUCKET_NAME")

    print(bucket)

    return "Done"
```

---

# 18. Why Use Environment Variables?

Suppose we have three environments:

```text
Development
Testing
Production
```

Each environment can have different configuration values.

For example:

```text
Development:
BUCKET_NAME = dev-bucket

Production:
BUCKET_NAME = prod-bucket
```

The same application code can use the environment variable.

Conceptually:

```text
Application Code
       ↓
Environment Variable
       ↓
Environment-specific Configuration
```

---

# 19. Environment Variables Example

Suppose we configure:

```text
ENVIRONMENT = development
```

Python code:

```python
import os

def lambda_handler(event, context):

    environment = os.environ.get("ENVIRONMENT")

    return {
        "statusCode": 200,
        "body": f"Environment: {environment}"
    }
```

The function can return:

```text
Environment: development
```

---

# 20. Secrets and Environment Variables

Environment variables are useful for configuration.

However, sensitive secrets such as:

* Database passwords
* API secrets
* Private keys

should not be carelessly stored as plain environment variables.

For sensitive secrets, AWS services such as:

* AWS Secrets Manager
* AWS Systems Manager Parameter Store

can be used depending on the application requirements.

---

# 21. Lambda Timeout

The **timeout** defines the maximum amount of time a Lambda invocation is allowed to run.

For example:

```text
Timeout = 10 seconds
```

If the function continues running beyond the configured timeout, the invocation is terminated.

Conceptually:

```text
Lambda Starts
     ↓
Code Executes
     ↓
10 Seconds
     ↓
Still Running?
     ↓
Lambda Terminates Invocation
```

---

# 22. Timeout Example

Suppose:

```text
Lambda Timeout = 10 seconds
```

Function execution time:

```text
4 seconds
```

Result:

```text
Success
```

If execution takes:

```text
15 seconds
```

Result:

```text
Timeout
```

Therefore:

```text
Execution Time
      <
Configured Timeout
```

is generally necessary for the invocation to complete successfully.

---

# 23. Lambda Memory

Lambda allows us to configure the amount of memory allocated to a function.

Examples include:

```text
128 MB
256 MB
512 MB
1024 MB
```

The currently supported range can change, so check AWS documentation for current limits.

---

# 24. Why Memory Matters

Lambda memory affects more than just RAM.

The compute resources allocated to the function are tied to the configured memory.

Generally:

```text
More Memory
     ↓
More Compute Capacity
     ↓
Potentially Faster Execution
```

However:

```text
More Memory
     ↓
Potentially Higher Cost per Unit Time
```

Therefore, we should test the function and choose an appropriate configuration.

---

# 25. Memory and Performance

Suppose a function is CPU-intensive.

With:

```text
256 MB
```

it may take longer.

We can test:

```text
512 MB
```

or:

```text
1024 MB
```

and compare:

* Execution duration
* Performance
* Cost

The goal is to find an appropriate balance between:

```text
Performance
+
Cost
```

---

# 26. Timeout vs Memory

These settings are different.

### Memory

Controls the memory and associated compute resources allocated to the function.

### Timeout

Controls how long the invocation can execute.

Example:

```text
Memory = 512 MB
Timeout = 30 seconds
```

This means:

```text
Allocated Memory:
512 MB

Maximum Execution Time:
30 seconds
```

---

# 27. Lambda Layers

A **Lambda Layer** is a package containing additional code, libraries, or dependencies that can be shared by Lambda functions.

Instead of including the same dependency inside every Lambda function, we can place shared dependencies into a Layer.

Conceptually:

```text
Lambda Function
      |
      +---- Application Code
      |
      +---- Lambda Layer
              |
              +---- Libraries
              +---- Dependencies
```

---

# 28. Why Use Lambda Layers?

Suppose we have:

```text
Function A
Function B
Function C
```

and all three functions use the same dependency.

Without a Layer:

```text
Function A → Dependency
Function B → Dependency
Function C → Dependency
```

With a Layer:

```text
              Shared Layer
             /     |     \
            ↓      ↓      ↓
       Function A Function B Function C
```

The dependency can be maintained separately.

---

# 29. Important Layer Concept

A Lambda Layer is **not a Lambda function**.

It is an additional package of reusable code or dependencies.

Remember:

```text
Lambda Function
=
Application Code

Lambda Layer
=
Reusable Dependencies / Shared Code
```

---

# 30. CloudWatch Logs

AWS Lambda integrates with **Amazon CloudWatch Logs**.

When Lambda executes, application logs and execution information can be available in CloudWatch Logs.

Example:

```python
def lambda_handler(event, context):

    print("Lambda started")

    print(event)

    return "Success"
```

The output from `print()` can appear in the Lambda function's CloudWatch log stream.

---

# 31. Lambda Logging Architecture

```text
Lambda
   |
   | print()
   ↓
CloudWatch Logs
   |
   +---- Log Group
   |
   +---- Log Streams
```

CloudWatch Logs are very important for troubleshooting.

---

# 32. CloudWatch Log Group

Lambda functions use a CloudWatch Logs log group.

A common naming pattern is:

```text
/aws/lambda/<function-name>
```

For example:

```text
/aws/lambda/my-first-lambda
```

The log group contains log streams.

---

# 33. CloudWatch Log Stream

A **log stream** contains a sequence of log events from Lambda execution environments.

Conceptually:

```text
Log Group
    |
    +---- Log Stream
    |
    +---- Log Stream
    |
    +---- Log Stream
```

Log events contain timestamped information about execution.

---

# 34. Why CloudWatch Logs Are Important

CloudWatch Logs help us troubleshoot:

* Application errors
* Exceptions
* Debug messages
* Input events
* Permission problems
* Timeout problems
* Application behavior
* Execution problems

For example:

```python
def lambda_handler(event, context):

    print("Function started")

    result = 10 / 0

    return result
```

This produces an error.

We can inspect the Lambda execution logs in CloudWatch.

---

# 35. API Gateway Integration

One of the most common Lambda architectures is:

```text
Client
  ↓
API Gateway
  ↓
Lambda
  ↓
Application Logic
  ↓
Response
  ↓
API Gateway
  ↓
Client
```

API Gateway provides an HTTP/HTTPS interface.

Lambda executes the backend application logic.

---

# 36. Why Use API Gateway with Lambda?

Lambda functions can be invoked by many services.

API Gateway is commonly used when we want an HTTP API to invoke Lambda.

For example:

```text
GET /hello
```

The request goes to:

```text
API Gateway
     ↓
Lambda
```

Lambda processes the request and returns a response.

---

# 37. API Gateway Example

Lambda code:

```python
def lambda_handler(event, context):

    return {
        "statusCode": 200,
        "body": "Hello from Lambda"
    }
```

API Gateway route:

```text
GET /hello
```

Architecture:

```text
Browser
   |
   | GET /hello
   ↓
API Gateway
   |
   ↓
Lambda
   |
   ↓
lambda_handler()
   |
   ↓
Response
   |
   ↓
API Gateway
   |
   ↓
Browser
```

---

# 38. API Gateway Event

When API Gateway invokes Lambda, the Lambda `event` contains information about the HTTP request.

Depending on the API type and integration, the event can contain information such as:

```text
HTTP Method
Path
Headers
Query Parameters
Path Parameters
Request Body
```

Example:

```python
def lambda_handler(event, context):

    print(event)

    return {
        "statusCode": 200,
        "body": "Request received"
    }
```

---

# 39. Lambda Response to API Gateway

A Lambda function commonly returns a response containing:

```python
return {
    "statusCode": 200,
    "body": "Hello World"
}
```

Here:

```text
statusCode
     ↓
HTTP status code

body
     ↓
Response content
```

---

# 40. Common HTTP Status Codes

Important HTTP status codes:

```text
200 → OK
201 → Created
400 → Bad Request
401 → Unauthorized
403 → Forbidden
404 → Not Found
500 → Internal Server Error
```

Example:

```python
return {
    "statusCode": 404,
    "body": "User not found"
}
```

---

# 41. Lambda Invocation

A Lambda function can be invoked by many different sources.

Examples:

```text
API Gateway
S3
EventBridge
SQS
SNS
Scheduled Events
Other AWS Services
AWS SDK
AWS CLI
```

Conceptually:

```text
S3 ──────────┐
             |
API Gateway ─┤
             |
EventBridge ─┤──→ Lambda
             |
SQS ─────────┘
```

---

# 42. Event-Driven Architecture

Lambda is especially useful for **event-driven architecture**.

The basic concept is:

```text
Something Happens
       ↓
Event Generated
       ↓
Lambda Triggered
       ↓
Lambda Processes Event
```

Example:

```text
File Uploaded to S3
       ↓
S3 Event
       ↓
Lambda
       ↓
Process File
```

Another example:

```text
HTTP Request
       ↓
API Gateway
       ↓
Lambda
       ↓
Process Request
```

---

# 43. Example — S3 + Lambda

Suppose a user uploads:

```text
photo.jpg
```

to an S3 bucket.

Architecture:

```text
User
 ↓
S3 Bucket
 ↓
Object Created Event
 ↓
Lambda
 ↓
Process Image
```

Lambda could:

* Validate the file
* Process the image
* Extract metadata
* Resize an image
* Store information in a database
* Send a notification

---

# 44. Example — API Gateway + Lambda + DynamoDB

A common serverless architecture is:

```text
Client
   ↓
API Gateway
   ↓
Lambda
   ↓
DynamoDB
```

For example:

```text
GET /users
     ↓
API Gateway
     ↓
Lambda
     ↓
DynamoDB
     ↓
Users
```

Lambda contains the application logic.

---

# 45. Example Lambda Function

A simple Python Lambda function:

```python
def lambda_handler(event, context):

    print("Lambda function started")

    name = event.get("name", "World")

    return {
        "statusCode": 200,
        "body": f"Hello, {name}!"
    }
```

Example event:

```json
{
    "name": "Adi"
}
```

The function can return:

```text
Hello, Adi!
```

---

# 46. Complete Lambda Architecture

```text
                    Client
                       |
                       ↓
                 API Gateway
                       |
                       ↓
                Lambda Function
                       |
       +---------------+---------------+
       |               |               |
    Runtime          Handler        Environment
                                      Variables
       |               |
       +-------+-------+
               |
               ↓
          Lambda Code
               |
       +-------+-------+
       |               |
 Execution Role      Layers
       |               |
       ↓               ↓
 AWS Services      Dependencies
       |
       ↓
 CloudWatch Logs
```

---

# 47. Lambda Function Lifecycle

A basic Lambda execution flow is:

```text
Event Occurs
     ↓
Lambda Invoked
     ↓
Execution Environment Available/Created
     ↓
Runtime Initialized if Needed
     ↓
Handler Executes
     ↓
Code Processes Event
     ↓
Response Returned
     ↓
Logs Available in CloudWatch
```

AWS manages the underlying compute infrastructure.

---

# 48. Lambda Troubleshooting

If a Lambda function is not working, check:

```text
1. Lambda invocation
        ↓
2. CloudWatch Logs
        ↓
3. Application errors
        ↓
4. Timeout
        ↓
5. Memory
        ↓
6. Execution Role
        ↓
7. Environment Variables
        ↓
8. Event/Input
        ↓
9. API Gateway configuration
```

This is a useful DevOps troubleshooting process.

---

# 49. Important Lambda Terms

| Term                  | Meaning                                                           |
| --------------------- | ----------------------------------------------------------------- |
| Serverless            | AWS manages the underlying infrastructure                         |
| Lambda Function       | Code executed by AWS Lambda                                       |
| Runtime               | Environment used to execute the code                              |
| Handler               | Function Lambda invokes                                           |
| Event                 | Input/trigger information                                         |
| Context               | Information about the current execution                           |
| Execution Role        | IAM role that gives Lambda permissions                            |
| Environment Variables | Configuration values available to the function                    |
| Timeout               | Maximum execution duration for an invocation                      |
| Memory                | Memory and associated compute resources allocated to the function |
| Layer                 | Reusable code/dependencies                                        |
| CloudWatch Logs       | Logs used to monitor and troubleshoot Lambda                      |
| API Gateway           | HTTP/API service that can invoke Lambda                           |

---

# 50. Step-by-Step Practical Lambda Lab

## Step 1 — Open AWS Lambda

Open the AWS Console.

Go to:

```text
AWS Console
    ↓
Lambda
```

---

## Step 2 — Create a Function

Select:

```text
Create function
```

---

## Step 3 — Choose Author From Scratch

Select:

```text
Author from scratch
```

---

## Step 4 — Enter Function Name

Example:

```text
my-first-lambda
```

---

## Step 5 — Select Runtime

Choose a supported runtime, for example:

```text
Python
```

Select an available supported Python version.

---

## Step 6 — Configure Execution Role

Choose or create an appropriate execution role.

For a basic Lambda function, the role should have the permissions required for logging and any AWS services that the function needs to access.

---

## Step 7 — Create Function

Click:

```text
Create function
```

AWS creates the Lambda function.

---

# 51. Write Lambda Code

In the Lambda code editor, use:

```python
def lambda_handler(event, context):

    print("Hello from AWS Lambda")

    return {
        "statusCode": 200,
        "body": "Hello World"
    }
```

---

# 52. Deploy the Function

After modifying the code, select:

```text
Deploy
```

This updates the deployed function code.

---

# 53. Test the Function

Select:

```text
Test
```

Create a test event.

Example:

```json
{
    "name": "Adi"
}
```

Invoke the function.

You should see the execution result.

---

# 54. Check CloudWatch Logs

After invoking the function:

```text
Lambda
   ↓
Monitor
   ↓
CloudWatch Logs
```

Check the logs for:

* Function execution
* `print()` output
* Errors
* Duration
* Request ID
* Execution information

---

# 55. Configure Environment Variables

Open the Lambda configuration settings.

Go to environment variables.

Add:

```text
Key:
ENVIRONMENT

Value:
development
```

Python code:

```python
import os

def lambda_handler(event, context):

    environment = os.environ.get("ENVIRONMENT")

    print(environment)

    return {
        "statusCode": 200,
        "body": "Lambda executed successfully"
    }
```

---

# 56. Configure Memory

Open the Lambda configuration for general settings.

Configure memory according to the workload.

For learning, you can test different values and compare:

```text
Execution Duration
Performance
Cost
```

---

# 57. Configure Timeout

Configure the Lambda timeout.

For example:

```text
10 seconds
```

Test the function and observe what happens when execution takes longer than the configured timeout.

---

# 58. API Gateway Integration Lab

For an HTTP API, the basic architecture is:

```text
Client
  ↓
API Gateway
  ↓
Lambda
```

Basic process:

```text
API Gateway
     ↓
Create API
     ↓
Create Route
     ↓
Connect Lambda
     ↓
Deploy API
```

Example route:

```text
GET /hello
```

can invoke:

```text
my-first-lambda
```

---

# 59. Complete Serverless Architecture

A common serverless application can look like:

```text
                 User
                  |
                  ↓
             API Gateway
                  |
                  ↓
               Lambda
                  |
        +---------+---------+
        |                   |
        ↓                   ↓
   DynamoDB               S3
        |
        ↓
      Data

Lambda
   |
   +---- Execution Role
   |
   +---- Environment Variables
   |
   +---- Layers
   |
   ↓
CloudWatch Logs
```

---

# 60. Lambda in DevOps

AWS Lambda is important for DevOps because Lambda deployments can be automated.

Lambda can be managed using:

* AWS Console
* AWS CLI
* Terraform
* CloudFormation
* CI/CD pipelines
* GitHub Actions
* Jenkins

Example:

```text
Developer
    ↓
Git Push
    ↓
CI/CD Pipeline
    ↓
AWS CLI / Infrastructure Tool
    ↓
Lambda
    ↓
New Version Deployed
```

---

# 61. Important Lambda Concepts to Remember

## Serverless

AWS manages the underlying infrastructure.

```text
You manage:
Code + Configuration

AWS manages:
Infrastructure
```

---

## Lambda Function

The code that AWS executes.

```python
def lambda_handler(event, context):
    return "Hello"
```

---

## Runtime

The environment used to execute your code.

```text
Python Runtime
Node.js Runtime
Java Runtime
etc.
```

---

## Handler

The function Lambda calls.

```text
filename.function_name
```

Example:

```text
lambda_function.lambda_handler
```

---

## Event

The input/trigger information.

```text
S3 Event
API Gateway Event
SQS Event
EventBridge Event
etc.
```

---

## Execution Role

IAM role that gives Lambda permissions.

```text
Lambda
 ↓
Execution Role
 ↓
AWS Permissions
 ↓
AWS Services
```

---

## Environment Variables

Configuration values available to the function.

```text
BUCKET_NAME
ENVIRONMENT
API_URL
```

---

## Timeout

Maximum execution time for an invocation.

```text
Execution Time > Timeout
        ↓
Invocation terminated
```

---

## Memory

Memory and associated compute resources allocated to the function.

```text
More Memory
     ↓
More Compute Capacity
```

---

## Layers

Reusable dependencies/shared code.

```text
Lambda
   +
Layer
```

---

## CloudWatch Logs

Used to view Lambda execution logs and troubleshoot problems.

```text
Lambda
  ↓
CloudWatch Logs
```

---

## API Gateway

Provides an HTTP/HTTPS API that can invoke Lambda.

```text
Client
  ↓
API Gateway
  ↓
Lambda
  ↓
Response
```

---

# 62. Lambda Mental Model

Remember Lambda using this flow:

```text
EVENT
  ↓
LAMBDA
  ↓
RUNTIME
  ↓
HANDLER
  ↓
CODE
  ↓
EXECUTION ROLE
  ↓
AWS SERVICES
  ↓
CLOUDWATCH LOGS
```

For an API:

```text
CLIENT
  ↓
API GATEWAY
  ↓
LAMBDA
  ↓
RESPONSE
```

---

# 63. Practice Checklist

Before completing Day 13, make sure you understand:

## Serverless

* [ ] What serverless means
* [ ] Serverless vs traditional servers
* [ ] EC2 vs Lambda
* [ ] Benefits of serverless

## Lambda Function

* [ ] What a Lambda function is
* [ ] How Lambda executes code
* [ ] Basic Lambda Python function

## Runtime

* [ ] What a runtime is
* [ ] Why runtime is required
* [ ] Different Lambda runtimes

## Handler

* [ ] What a handler is
* [ ] Handler syntax
* [ ] `filename.function_name`

## Event

* [ ] What an event is
* [ ] API Gateway events
* [ ] S3 events
* [ ] Event-driven architecture

## Execution Role

* [ ] What an execution role is
* [ ] IAM permissions
* [ ] Least privilege
* [ ] Lambda accessing AWS services

## Environment Variables

* [ ] What environment variables are
* [ ] Why they are useful
* [ ] How Python reads them
* [ ] Difference between configuration and secrets

## Timeout

* [ ] What timeout means
* [ ] Why timeout matters
* [ ] What happens when timeout is exceeded

## Memory

* [ ] What Lambda memory means
* [ ] Relationship between memory and compute
* [ ] Performance vs cost

## Layers

* [ ] What Lambda Layers are
* [ ] Why Layers are useful
* [ ] Shared dependencies

## CloudWatch Logs

* [ ] Lambda logging
* [ ] CloudWatch Log Groups
* [ ] CloudWatch Log Streams
* [ ] Troubleshooting using logs

## API Gateway

* [ ] What API Gateway is
* [ ] API Gateway + Lambda architecture
* [ ] HTTP requests
* [ ] Lambda HTTP responses
* [ ] Status codes

---

# 64. Final Lambda Architecture

```text
                         USER
                           |
                           ↓
                    API GATEWAY
                           |
                           ↓
                   LAMBDA FUNCTION
                           |
             +-------------+-------------+
             |             |             |
             ↓             ↓             ↓
          RUNTIME       HANDLER      ENVIRONMENT
                                         VARIABLES
             |             |
             +------+------+
                    |
                    ↓
                LAMBDA CODE
                    |
          +---------+---------+
          |                   |
          ↓                   ↓
    EXECUTION ROLE          LAYERS
          |                   |
          ↓                   ↓
    AWS SERVICES        SHARED DEPENDENCIES
          |
          ↓
   CLOUDWATCH LOGS
```

---

# ⭐ Final Memory Trick

```text
Serverless
     ↓
Lambda Function
     ↓
Runtime
     ↓
Handler
     ↓
Event
     ↓
Execution Role
     ↓
Environment Variables
     ↓
Memory + Timeout
     ↓
Layers
     ↓
CloudWatch Logs
```

For APIs:

```text
Client
   ↓
API Gateway
   ↓
Lambda
   ↓
Application Logic
   ↓
Response
```

**AWS Lambda = Run code in response to events without managing the underlying servers.**

---

# Day 13 Summary

AWS Lambda is a serverless compute service that executes code in response to events.

The most important concepts are:

```text
Serverless
Lambda Function
Runtime
Handler
Event
Execution Role
Environment Variables
Timeout
Memory
Layers
CloudWatch Logs
API Gateway
```

These concepts form the foundation for building and deploying **serverless applications on AWS** and are especially important for **DevOps, Cloud Engineering, automation, and CI/CD**.

