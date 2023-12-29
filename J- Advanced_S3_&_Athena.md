 ### Amazon S3 – Moving between Storage Classes
- You can transition objects between storage classes
- For infrequently accessed object, move them to **Standard IA**
- For archive objects that you don’t need fast access to, move them to **Glacier or Glacier Deep Archive**
- Moving objects can be automated using a **Lifecycle Rules**

### Amazon S3 – Lifecycle Rules
- **Transition Actions** – configure objects to transition to another storage class
    - Move objects to Standard IA class 60 days after creation
    - Move to Glacier for archiving after 6 months
- **Expiration actions** – configure objects to expire (delete) after some time
    - Access log files can be set to delete after a 365 days
    - **Can be used to delete old versions of files (if versioning is enabled)**
    - Can be used to delete incomplete Multi-Part uploads

- Rules can be created for a certain prefix (example: s3://mybucket/mp3/*)
- Rules can be created for certain objects Tags (example: Department: Finance)

### Amazon S3 – Lifecycle Rules (Scenario 1)
- **Your application on EC2 creates images thumbnails after profile photos are uploaded to Amazon S3. These thumbnails can be easily recreated, and only need to be kept for 60 days. The source images should be able to be immediately retrieved for these 60 days, and afterwards, the user can wait up to 6 hours. How would you design this?**
- S3 source images can be on **Standard**, with a lifecycle configuration to transition them to **Glacier** after 60 days
- S3 thumbnails can be on **One-Zone IA**, with a lifecycle configuration to expire them (delete them) after 60 days

### Amazon S3 – Lifecycle Rules (Scenario 2)
- **A rule in your company states that you should be able to recover your deleted S3 objects immediately for 30 days, although this may happen rarely. After this time, and for up to 365 days, deleted objects should be recoverable within 48 hours.**
- **Enable S3 Versioning** in order to have object versions, so that “deleted objects” are in fact hidden by a “delete marker” and can be recovered
- Transition the “noncurrent versions” of the object to **Standard IA**
- Transition afterwards the “noncurrent versions” to **Glacier Deep Archive**

### Amazon S3 Analytics – Storage Class Analysis
- Help you decide when to transition objects to the right storage class
- Recommendations for **Standard** and **Standard IA**
    - Does NOT work for One-Zone IA or Glacier
- Report is updated daily
- 24 to 48 hours to start seeing data analysis
- Good first step to put together Lifecycle Rules (or improve them)!

### S3 Event Notifications
- S3:ObjectCreated, S3:ObjectRemoved, S3:ObjectRestore, S3:Replication…
- Object name filtering possible (*.jpg)
- Use case: generate thumbnails of images uploaded to S3
- **Can create as many “S3 events” as desired**

- S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer

### S3 Event Notifications
- **Advanced filtering** options with JSON rules (metadata, object size, name...)
- **Multiple Destinations** – ex Step Functions, Kinesis Streams / Firehose…
- **EventBridge Capabilities** – Archive, Replay Events, Reliable delivery

### S3 – Baseline Performance
- Amazon S3 automatically scales to high request rates, latency 100-200 ms
- Your application can achieve at least **3,500 PUT/COPY/POST/DELETE or 5,500 GET/HEAD requests per second per prefix in a bucket.**
- There are no limits to the number of prefixes in a bucket.
- Example (object path => prefix):
    - bucket/folder1/sub1/file => /folder1/sub1/
    - bucket/folder1/sub2/file => /folder1/sub2/
    - bucket/1/file            => /1/
    - bucket/2/file            => /2/
- If you spread reads across all four prefixes evenly, you can achieve 22,000 requests per second for GET and HEAD

### S3 Performance
- **Multi-Part upload:**
- recommended for files > 100MB, must use for files > 5GB
- Can help parallelize uploads (speed up transfers)

- **S3 Transfer Acceleration**
This is used to upload files on-prem to s3 bucket
- Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in the target region
- Compatible with multi-part upload

### S3 Performance – S3 Byte-Range Fetches
- Parallelize GETs by requesting specific byte ranges
- Better resilience in case of failures
Can be used to speed up downloads

Can be used to retrieve only partial data (for example the head of a file)

### S3 Select & Glacier Select
- Retrieve less data using SQL by performing **server-side filtering**
- Can filter by rows & columns (simple SQL statements)
- Less network transfer, less CPU cost client-side

### S3 Batch Operations
- Perform bulk operations on existing S3 objects with a single request, example:
    - Modify object metadata & properties
    - Copy objects between S3 buckets
    - **Encrypt un-encrypted objects**
    - Modify ACLs, tags
    - Restore objects from S3 Glacier
    - Invoke Lambda function to perform custom action on each object
- A job consists of a list of objects, the action to perform, and optional parameters
- S3 Batch Operations manages retries, tracks progress, sends completion notifications, generate reports …
- **You can use S3 Inventory to get object list and use S3 Select to filter your objects**

### S3 Inventory
- List objects and their corresponding metadata (alternative to S3 **List** API operation)
- Usage examples:
    - Audit and report on the replication and encryption status of your objects
    - Get the number of objects in an S3 bucket
    - Identify the total storage of previous object versions
- Generate daily or weekly reports
- Output files: CSV, ORC, or Apache Parquet
- You can query all the data using Amazon Athena, Redshift, Presto, Hive, Spark…
- You can filter generated report using S3 Select
- Use cases: Business, Compliance, Regulatory needs, …

### Amazon S3 Glacier
- Low-cost object storage meant for archiving / backup
- Data is retained for the longer term (10s of years)
- Alternative to on-premises magnetic tape storage
- Average annual durability is 99.999999999%
- Cost per storage per month ($0.004 / GB – Standard | $0.00099 / GB Deep Archive)

- Each item in Glacier is called **“Archive”** (up to 40TB)
- Archives are stored in **”Vaults”**
- By default, data encrypted at rest using AES-256 – keys managed by AWS

- Exam tip: archival from S3 after XXX days => use Glacier and backup lifecycle policy

### Amazon S3 Glacier Operations
- **Vault Operations:**
    - **Create & Delete** – delete only when there’s no archives in it
    - **Retrieving Metadata** – creation date, number of archives, total size of all archives, …
    - **Download Inventory** – list of archives in the vault (archive ID, creation date, size, …)
- **Glacier Operations:**
    - **Upload** - single operation or by parts (MultiPart upload) for larger archives
    - **Download** - first initiate a retrieval job for the archive, Glacier then prepares it for download. User then has a limited time to download the data from staging server. (optionally, specify a range or portion of bytes to retrieve)
    - **Delete** - use Glacier Rest API or AWS SDKs by specifying archive ID
- Restore links have an expiry date
- **Retrieval Options:**
    - **Expedited (1 to 5 minutes retrieval)** – $0.03 per GB and $10 per 1000 requests
    - **Standard (3 to 5 hours)** - $0.01 per GB and 0.03 per 1000 requests
    - **Bulk (5 to 12 hours)** - $0.0025 per GB and $0.025 per 1000 requests

### Amazon S3 Glacier - Vault Policies & Vault Lock
- Each Vault has:
    - ONE vault access policy
    - ONE vault lock policy
- Vault Policies are written in JSON
- Vault Access Policy is like a s3 bucket policy (restrict user / account permissions)
- Vault Lock Policy is a policy you lock, for regulatory and compliance requirements.
    - The policy is immutable, **it can never be changed (that’s why it’s call LOCK)**
    - Example 1: forbid deleting an archive if less than 1 year old
    - Example 2: implement WORM policy (write once read many)

### Glacier – Notifications for Restore Operations
- **Vault Notification Configuration**
    - Configure a vault so that when a job completes, a message is sent to SNS
    - Optionally, specify an SNS topic when you initiate a job
- **S3 Event Notifications**
    - S3 supports the restoration of objects archived to S3 Glacier storage classes
    - s3:ObjectRestore:Post => notify when object restoration initiated
    - s3:ObjectRestore:Completed => notify when object restoration completed

### S3 Multi Part Upload – Deep Dive
- Upload large objects in parts (any order)
- Recommended for files > 100MB, must use for files > 5GB
- Can help parallelize uploads (speed up transfers)
- Max. parts: 10,000
- **Failures:** restart uploading ONLY failed parts (improves performance)
- Use **Lifecycle Policy to automate old parts deletion** of unfinished upload after x days (e.g., network outage)
- Upload using AWS CLI or AWS SDK

### Amazon Athena ***
- **Serverless** query service to analyze data stored in Amazon S3
- Uses standard SQL language to query the files (built on Presto)
- Supports CSV, JSON, ORC, Avro, and Parquet
- Pricing: $5.00 per TB of data scanned
- Commonly used with Amazon Quicksight for reporting/dashboards
- **Use cases:** Business intelligence / analytics / reporting, analyze & query VPC Flow Logs, ELB Logs, **CloudTrail trails**, etc...
- Exam Tip: analyze data in S3 using serverless SQL, you should think about Athena

### Amazon Athena – Performance Improvement ***
- Use **columnar data** for cost-savings (less scan)
    - Apache Parquet or ORC is recommended
    - Huge performance improvement
    - Use Glue to convert your data to Parquet or ORC
- **Compress data** for smaller retrievals (bzip2, gzip, lz4, snappy, zlip, zstd…)
- **Partition** datasets in S3 for easy querying on virtual columns
    - s3://yourBucket/pathToTable
                    /<PARTITION_COLUMN_NAME>=<VALUE>
                        /<PARTITION_COLUMN_NAME>=<VALUE>
                            /<PARTITION_COLUMN_NAME>=<VALUE>
                                /etc…
-   Example: s3://athena-examples/flight/parquet/year=1991/month=1/day=1/
- **Use larger files** (> 128 MB) to minimize overhead because larger files are easy to scan and retrieve

### Amazon Athena – Federated Query
- Allows you to run SQL queries across data stored in relational, non-relational, object, and custom data sources (AWS or on-premises)
- Uses Data Source Connectors that run on AWS Lambda to run Federated Queries (e.g., CloudWatch Logs, DynamoDB, RDS, …)
- Store the results back in Amazon S3