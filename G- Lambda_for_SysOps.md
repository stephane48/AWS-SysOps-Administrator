### Why AWS Lambda
Amazon EC2
- Virtual Servers in the Cloud
- Limited by RAM and CPU
- Continuously running
- Scaling means intervention to add / remove servers

Amazon Lambda
- Virtual **functions** – no servers to manage!
- Limited by time - **short executions** for up to 15 min, and run on-demand (billed only when it runs)
- Run **on-demand**
- **Scaling is automated!**

### Benefits of AWS Lambda
- Easy Pricing:
    - Pay per request and compute time
    - Free tier of 1,000,000 AWS Lambda requests and 400,000 GBs of compute time
- Integrated with the whole AWS suite of services
- Integrated with many programming languages
- Easy monitoring through AWS CloudWatch
- Easy to get more resources per functions (up to 10GB of RAM!)
- Increasing RAM will also improve CPU and network!

### AWS Lambda language support
- Node.js (JavaScript)
- Python
- Java (Java 8 compatible)
- C# (.NET Core)
- Golang
- C# / Powershell
- Ruby
- Custom Runtime API (community supported, example Rust)
- Lambda Container Image
    - The container image must implement the Lambda Runtime API
    - ECS / Fargate is preferred for running arbitrary Docker images

### AWS Lambda Pricing: example
- You can find overall pricing information here:https://aws.amazon.com/lambda/pricing/
- Pay per calls:
    - First 1,000,000 requests are free
    - $0.20 per 1 million requests thereafter ($0.0000002 per request)
- Pay per duration: (in increment of 1 ms)
    - 400,000 GB-seconds of compute time per month for FREE
    - == 400,000 seconds if function is 1GB RAM
    - == 3,200,000 seconds if function is 128 MB RAM
    - After that $1.00 for 600,000 GB-seconds
- It is usually very cheap to run AWS Lambda so it’s very popular

### CloudWatch Events / EventBridge ***
Demo

### S3 Events Notifications
- S3:ObjectCreated, S3:ObjectRemoved, S3:ObjectRestore, S3:Replication…
- Object name filtering possible (*.jpg)
- Use case: generate thumbnails of images uploaded to S3
- S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer
- If two writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent
- If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket.

### Lambda Execution Role (IAM Role)
- Grants the Lambda function permissions to AWS services / resources
- Sample managed policies for Lambda:
    - AWSLambdaBasicExecutionRole – Upload logs to CloudWatch.
    - AWSLambdaKinesisExecutionRole – Read from Kinesis
    - AWSLambdaDynamoDBExecutionRole – Read from DynamoDB Streams
    - AWSLambdaSQSQueueExecutionRole – Read from SQS
    - AWSLambdaVPCAccessExecutionRole – Deploy Lambda function in VPC
    - AWSXRayDaemonWriteAccess – Upload trace data to X-Ray.
- **When you use an event source mapping to invoke your function, Lambda uses the execution role to read event data.**
- Best practice: create one Lambda Execution Role per function

### Lambda Resource Based Policies
- Use resource-based policies to give other accounts and AWS services permission to use your Lambda resources
- Similar to S3 bucket policies for S3 bucket
- An IAM principal can access Lambda:
    - if the IAM policy attached to the principal authorizes it (e.g. user access)
    - OR if the resource-based policy authorizes (e.g. service access)
- When an AWS service like Amazon S3 calls your Lambda function, the resource-based policy gives it access.

### Lambda Logging & Monitoring
- Lambda has an integration with CloudWatch Logs:
    - AWS Lambda execution logs are stored in AWS CloudWatch Logs
    - **Make sure your AWS Lambda function has an execution role with an IAM policy that authorizes writes to CloudWatch Logs**
- CloudWatch Metrics:
    - AWS Lambda metrics are displayed in AWS CloudWatch Metrics
    - Invocations, Durations, Concurrent Executions
    - Error count, Success Rates, Throttles
    - Async Delivery Failures
    - Iterator Age (Kinesis & DynamoDB Streams)

### Lambda Tracing with X-Ray
- Enable in Lambda configuration **(Active Tracing)**
- Runs the X-Ray daemon for you
- Use AWS X-Ray SDK in Code
- Ensure Lambda Function has a correct IAM Execution Role to write into X-Ray
    - The managed policy is called AWSXRayDaemonWriteAccess
- Environment variables to communicate with X-Ray
    - **_X_AMZN_TRACE_ID**: contains the tracing header
    - **AWS_XRAY_CONTEXT_MISSING**: by default, LOG_ERROR
    - **AWS_XRAY_DAEMON_ADDRESS**: the X-Ray Daemon IP_ADDRESS:PORT (the most important for the exam)

### Lambda Function Configuration
- **RAM**:
    - From 128MB to 10GB in 1MB increments
    - The more RAM you add, the more vCPU credits you get
    - At 1,792 MB, a function has the equivalent of one full vCPU
    - After 1,792 MB, you get more than one CPU, and need to use multi-threading in your code to benefit from it (up to 6 vCPU)
- **If your application is CPU-bound (computation heavy), increase RAM**
- **Timeout**: default 3 seconds, maximum is 900 seconds (15 minutes)

### Lambda Execution Context
- The execution context is a temporary runtime environment that initializes any external dependencies of your lambda code
- Great for database connections, HTTP clients, SDK clients…
- The execution context is maintained for some time in anticipation of another Lambda function invocation
- The next function invocation can “re-use” the context to execution time and save time in initializing connections objects
- The execution context includes the */tmp* directory

### Initialize outside the handler
Anything that takes time to initialize, define them outside of you handler function

### Lambda Functions /tmp space
- If your Lambda function needs to download a big file to work…
- If your Lambda function needs disk space to perform operations…
- You can use the /tmp directory
- Max size is 10GB
- The directory content remains when the execution context is frozen, providing transient cache that can be used for multiple invocations (helpful to checkpoint your work)
- For permanent persistence of object (non temporary), use S3
- To encrypt content on /tmp, you must generate KMS Data Keys

### Lambda Concurrency and Throttling
- Concurrency limit: up to 1000 concurrent executions
- Can set a **“reserved concurrency”** at the function level (=limit)
- Each invocation over the concurrency limit will trigger a “Throttle”
- Throttle behavior:
    - If synchronous invocation => return ThrottleError - 429
    - If asynchronous invocation => retry automatically and then go to DLQ
- If you need a higher limit, open a support ticket

### Lambda Concurrency Issue
- If you don’t reserve (=limit) concurrency, the following can happen:

### Concurrency and Asynchronous Invocations
- If the function doesn't have enough concurrency available to process all events, additional requests are throttled.
- For throttling errors (429) and system errors (500-series), Lambda returns the event to the queue and attempts to run the function again for up to 6 hours.
- The retry interval increases exponentially from 1 second after the first attempt to a maximum of 5 minutes.

### Cold Starts & Provisioned Concurrency
- **Cold Start**:
    - New instance => code is loaded and code outside the handler run (init)
    - If the init is large (code, dependencies, SDK…) this process can take some time.
    - First request served by new instances has higher latency than the rest
- **Provisioned Concurrency**:
    - Concurrency is allocated before the function is invoked (in advance)
    - So the cold start never happens and all invocations have low latency
    - Application Auto Scaling can manage concurrency (schedule or target utilization)
- **Note:**
    - Note: cold starts in VPC have been dramatically reduced in Oct & Nov 2019
    - https://aws.amazon.com/blogs/compute/announcing-improved-vpc-networking-for-aws-lambda-functions/

### Lambda Monitoring – CloudWatch Metrics
- **Invocations** – number of times your function is invoked (success/failure)
- **Duration** – amount of time your function spends processing an event
- **Errors** – number of invocations that result in a function error
- **Throttles** – number of invocation requests that are throttled (no concurrency available)
- **DeadLetterErrors** – number of times Lambda failed to send an event to a DLQ (async invocations)
- **IteratorAge** – time between when a Stream receives a record and when the Event Source Mapping sends the event to the function (for Event Source Mapping that reads from Stream)
- **ConcurrentExecutions** – number of function instances that are processing events

### Lambda Monitoring – CloudWatch Alarms
- **Example 1** – No Lambda Invocations in the last hour using **Invocations** CloudWatch Metric
- **Example 2** – When **error > 0** using **Errors** CloudWatch Metric
- **Example 3** – When throttles > 0 using **Throttles** CloudWatch Metric

### Lambda Monitoring – Lambda Insights
- Collects, aggregates, and summarizes:
    - **System-level Metrics** – CPU time, memory, disk, network
    - **Diagnostic Information** – cold starts, Lambda worker shutdowns
- Helps you isolate issues with your Lambda functions and resolve them quickly
- Uses a CloudWatch Lambda Extension (provided as a Lambda layer)
