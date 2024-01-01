### AWS CloudWatch Metrics
- CloudWatch provides metrics for every services in AWS
- **Metric** is a variable to monitor (CPUUtilization, NetworkIn…)
- Metrics belong to **namespaces**
- **Dimension** is an attribute of a metric (instance id, environment, etc…).
- Up to 30 dimensions per metric
- Metrics have **timestamps**
- Can create CloudWatch dashboards of metrics

### EC2 Detailed monitoring
- EC2 instance metrics have metrics “every 5 minutes”
- With detailed monitoring (for a cost), you get data “every 1 minute”
- Use detailed monitoring if you want to scale faster for your ASG!

- The AWS Free Tier allows us to have 10 detailed monitoring metrics

- Note: EC2 Memory usage is by default not pushed (must be pushed from inside the instance as a custom metric)

### CloudWatch Custom Metrics
- Possibility to define and send your own custom metrics to CloudWatch
- Example: memory (RAM) usage, disk space, number of logged in users …
- Use API call **PutMetricData**
- Ability to use dimensions (attributes) to segment metrics
    - Instance.id
    - Environment.name
- Metric resolution (**StorageResolution** API parameter – two possible value):
    - Standard: 1 minute (60 seconds)
    - High Resolution: 1/5/10/30 second(s) – Higher cost
- **Important**: Accepts metric data points two weeks in the past and two hours in the future (make sure to configure your EC2 instance time correctly)

### CloudWatch Dashboards
- Great way to setup custom dashboards for quick access to key metrics and alarms
- **Dashboards are global**
- **Dashboards can include graphs from different AWS accounts and regions**
- You can change the time zone & time range of the dashboards
- You can setup automatic refresh (10s, 1m, 2m, 5m, 15m)
- Dashboards can be shared with people who don’t have an AWS account (public, email address, 3rd party SSO provider through Amazon Cognito)
- Pricing:
    - 3 dashboards (up to 50 metrics) for free
    - $3/dashboard/month afterwards

### CloudWatch Logs
- **Log groups:** arbitrary name, usually representing an application
- **Log stream:** instances within application / log files / containers
- Can define log expiration policies (never expire, 1 day to 10 years…)
- **CloudWatch Logs can send logs to:**
    - Amazon S3 (exports)
    - Kinesis Data Streams
    - Kinesis Data Firehose
    - AWS Lambda
    - OpenSearch
- Logs are encrypted by default
- Can setup KMS-based encryption with your own keys

### CloudWatch Logs - Sources
- SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
- Elastic Beanstalk: collection of logs from application
- ECS: collection from containers
- AWS Lambda: collection from function logs
- VPC Flow Logs: VPC specific logs
- API Gateway
- CloudTrail based on filter
- Route53: Log DNS queries

### CloudWatch Logs Insights
- Search and analyze log data stored in CloudWatch Logs
- Example: find a specific IP inside a log, count occurrences of “ERROR” in your logs…
- Provides a purpose-built query language
    - Automatically discovers fields from AWS services and JSON log events
    - Fetch desired event fields, filter based on conditions, calculate aggregate statistics, sort events, limit number of events…
    - Can save queries and add them to CloudWatch Dashboards
- Can query multiple Log Groups in different AWS accounts
- It’s a query engine, not a real-time engine

### CloudWatch Logs – S3 Export
- Log data can take **up to 12 hours** to become available for export
- The API call is **CreateExportTask**
- Not near-real time or real-time… use Logs Subscriptions instead

### CloudWatch Logs Subscriptions
- Get a real-time log events from CloudWatch Logs for processing and analysis
- Send to Kinesis Data Streams, Kinesis Data Firehose, or Lambda
- **Subscription Filter** – filter which logs are events delivered to your destination

### CloudWatch Logs Subscriptions
- Cross-Account Subscription – send log events to resources in a different AWS account (KDS, KDF)

### CloudWatch Alarms
- Alarms are used to trigger notifications for any metric
- Various options (sampling, %, max, min, etc…)
- Alarm States:
    - OK
    - INSUFFICIENT_DATA
    - ALARM
- Period:
    - Length of time in seconds to evaluate the metric
    - High resolution custom metrics: 10 sec, 30 sec or multiples of 60 sec

### CloudWatch Alarm Targets
- Stop, Terminate, Reboot, or Recover an EC2 Instance
- Trigger Auto Scaling Action
- Send notification to SNS (from which you can do pretty much anything)

### CloudWatch Alarms – Composite Alarms
- CloudWatch Alarms are on a single metric
- **Composite Alarms are monitoring the states of multiple other alarms**
- can use AND and OR conditions
- Helpful to reduce “alarm noise” by creating complex composite alarms

### EC2 Instance Recovery
- Status Check:
    - Instance status = check the EC2 VM
    - System status = check the underlying hardware
- Recovery: Same Private, Public, Elastic IP, metadata, placement group    

### CloudWatch Alarm: good to know
- Alarms can be created based on CloudWatch Logs Metrics Filters
- To test alarms and notifications, set the alarm state to Alarm using CLI **aws cloudwatch set-alarm-state --alarm-name "myalarm" --state-value ALARM --state-reason "testing purposes"**

### CloudWatch Synthetics Canary
- Configurable script that monitor your APIs, URLs, Websites, …
- Reproduce what your customers do programmatically to find issues before customers are impacted
- Checks the availability and latency of your endpoints and can store load time data and screenshots of the UI
- Integration with CloudWatch Alarms
- Scripts written in Node.js or Python
- Programmatic access to a headless Google Chrome browser
- Can run once or on a regular schedule

### CloudWatch Synthetics Canary Blueprints
- **Heartbeat Monitor** – load URL, store screenshot and an HTTP archive file
- **API Canary** – test basic read and write functions of REST APIs
- **Broken Link Checker** – check all links inside the URL that you are testing
- **Visual Monitoring** – compare a screenshot taken during a canary run with a baseline screenshot
- **Canary Recorder** – used with CloudWatch Synthetics Recorder (record your actions on a website and automatically generates a script for that)
- **GUI Workflow Builder** – verifies that actions can be taken on your webpage (e.g., test a webpage with a login form)

### Amazon EventBridge (formerly CloudWatch Events)
- Schedule: Cron jobs (scheduled scripts)
    **Schedule Every Hour => Trigger script on Lambda function**
- Event Pattern: Event rules to react to a service doing something
    **IAM Root User Sign in Event => SNS Topic with Email Notification**
- Trigger Lambda functions, send SQS/SNS messages…

### Amazon EventBridge Rules

### Amazon EventBridge
Default Event Bus uses by AWS services 
Partner Event Bus uses by AWS SaaS Partners like Zendesk or Datadog
Custom Event Bus uses by custom apps
- Event buses can be accessed by other AWS accounts using Resource-based Policies
- You can **archive events** (all/filter) sent to an event bus (indefinitely or set period)
- Ability to **replay archived events**

### Amazon EventBridge – Schema Registry
- EventBridge can analyze the events in your bus and infer the **schema**
- The **Schema Registry** allows you to generate code for your application, that will know in advance how data is structured in the event bus
- Schema can be versioned

### Amazon EventBridge – Resource-based Policy
- Manage permissions for a specific Event Bus
- Example: allow/deny events from another AWS account or AWS region
- Use case: aggregate all events from your AWS Organization in a single AWS account or AWS region

### Service Quotas CloudWatch Alarms
- Notify you when you’re close to a service quota value threshold
- Create CloudWatch Alarms on the Service Quotas console
- Example: Lambda concurrent executions
- Helps you know if you need to request a quota increase or shutdown resources before limit is reached

### Alternative: Trusted Advisor + CW Alarms
- Limited number of Service Limits checks in Trusted Advisor (~50)
- Trusted Advisor publishes its check results to CloudWatch
- You can create CloudWatch Alarms on service quota usage (Service Limits)

### AWS CloudTrail
- **Provides governance, compliance and audit for your AWS Account**
- CloudTrail is enabled by default!
- Get **an history of events / API calls made within your AWS Account** by:
    - Console
    - SDK
    - CLI
    - AWS Services
- Can put logs from CloudTrail into CloudWatch Logs or S3
- **A trail can be applied to All Regions (default) or a single Region.**
- If a resource is deleted in AWS, investigate CloudTrail first!

### CloudTrail Events
We have 3 kind of events in CloudTrail
- **Management Events:**
    - Operations that are performed on resources in your AWS account
    - Examples:
        - Configuring security (IAM **AttachRolePolicy**)
        - Configuring rules for routing data (Amazon EC2 **CreateSubnet**)
        - Setting up logging (AWS CloudTrail **CreateTrail**)
    - **By default, trails are configured to log management events.**
    - Can separate **Read Events** (that don’t modify resources) from **Write Events** (that may modify resources)
- **Data Events:**
    - **By default, data events are not logged (because high volume operations)**
    - Amazon S3 object-level activity (ex: GetObject, DeleteObject, PutObject): can separate Read and Write Events
    - AWS Lambda function execution activity (the **Invoke** API)
- **CloudTrail Insights Events:**
    - See next slide :)

### CloudTrail Insights
- Enable **CloudTrail Insights to detect unusual activity** in your account:
    - inaccurate resource provisioning
    - hitting service limits
    - Bursts of AWS IAM actions
    - Gaps in periodic maintenance activity
- CloudTrail Insights analyzes normal management events to create a baseline
- And then **continuously analyzes write events to detect unusual patterns**
    - Anomalies appear in the CloudTrail console
    - Event is sent to Amazon S3
    - An EventBridge event is generated (for automation needs)

### CloudTrail Events Retention
- Events are stored for 90 days in CloudTrail
- To keep events beyond this period, log them to S3 and use Athena

### Amazon EventBridge – Intercept API Calls

### Amazon EventBridge + CloudTrail

### CloudTrail – Log File Integrity Validation ***
- **Digest Files:**
    - References the log files for the last hour and contains a hash of each
    - Stored in the same S3 bucket as log files (different folder)
- Helps you determine whether a log file was modified/deleted after CloudTrail delivered it
- Hashing using SHA-256, Digital Signing using SHA- 256 with RSA
- Protect the S3 bucket using bucket policy, versioning, MFA Delete protection, encryption, object lock
- Protect CloudTrail using IAM

### CloudTrail – Integration with EventBridge
- Used to react to any API call being made in your account
- CloudTrail is not “real-time”:
    - Delivers an event within 15 minutes of an API call
    - Delivers log files to an S3 bucket every 5 minutes

### CloudTrail – Organizations Trails
- A trail that will log all events for all AWS accounts in an AWS Organization
- Log events for management and member accounts
- Trail with the same name will be created in every AWS account (IAM permissions)
- Member accounts can’t remove or modify the organization trail (view only)

### AWS Config
- Helps with auditing and recording **compliance** of your AWS resources based on rules you set
- Helps record configurations and changes over time
- Questions that can be solved by AWS Config:
    - Is there unrestricted SSH access to my security groups?
    - Do my buckets have any public access?
    - How has my ALB configuration changed over time?
- You can receive alerts (SNS notifications) for any changes
- AWS Config is a per-region service
- Can be aggregated across regions and accounts
- Possibility of storing the configuration data into S3 (analyzed by Athena)

### Config Rules
- Can use AWS managed config rules (over 75 rules)
- Can make custom config rules (must be defined in AWS Lambda)
    - Ex: evaluate if each EBS disk is of type gp2
    - Ex: evaluate if each EC2 instance is t2.micro
- Rules can be evaluated / triggered:
    - For each config change
    - And / or: at regular time intervals
- **AWS Config Rules does not prevent actions from happening (no deny)**

- Pricing: no free tier, $0.003 per configuration item recorded per region, $0.001 per config rule evaluation per region

### AWS Config Resource
- View compliance of a resource over time
- View configuration of a resource over time
- View CloudTrail API calls of a resource over time

### Config Rules – Remediations
- Automate remediation of non-compliant resources using SSM Automation Documents
- Use AWS-Managed Automation Documents or create custom Automation Documents
    - Tip: you can create custom Automation Documents that invokes Lambda function
- You can set **Remediation Retries** if the resource is still non-compliant after autoremediation

### Config Rules – Notifications
- Use EventBridge to trigger notifications when AWS resources are noncompliant
- Ability to send configuration changes and compliance state notifications to SNS (all events – use SNS Filtering or filter at client-side)

### AWS Config – Aggregators ***
- The aggregator is created in one central aggregator account to view all the compliant & non-compliant resources
- To view all the Aggregates rules, resources, etc… across multiple accounts & regions
- If using AWS Organizations, no need for individual Authorization
- Rules are created in each individual source AWS account
- Can deploy rules to multiple target accounts using CloudFormation StackSets

### CloudWatch vs CloudTrail vs Config ***
- CloudWatch
    - Performance monitoring (metrics, CPU, network, etc…) & dashboards
    - Events & Alerting
    - Log Aggregation & Analysis
- CloudTrail
    - Record API calls made within your Account by everyone
    - Can define trails for specific resources
    - Global Service
- Config
    - Record configuration changes
    - Evaluate resources against compliance rules
    - Get timeline of changes and compliance

### For an Elastic Load Balancer
- CloudWatch:
    - Monitoring Incoming connections metric
    - Visualize error codes as a % over time
    - Make a dashboard to get an idea of your load balancer performance
- Config:
    - Track security group rules for the Load Balancer
    - Track configuration changes for the Load Balancer to see if someone modifies the SSL certificate, etc...
    - Ensure an SSL certificate is always assigned to the Load Balancer (compliance) like non un-encrypted traffic on the ELB
- CloudTrail:
    - Track who made any changes to the Load Balancer with API calls