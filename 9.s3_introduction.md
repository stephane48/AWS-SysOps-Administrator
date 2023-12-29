### Section introduction
- Amazon S3 is one of the main building blocks of AWS
- It’s advertised as ”infinitely scaling” storage
- Many websites use Amazon S3 as a backbone
- Many AWS services use Amazon S3 as an integration as well
- We’ll have a step-by-step approach to S3

### Amazon S3 Use cases
- Backup and storage
- Disaster Recovery
- Archive
- Hybrid Cloud storage
- Application hosting
- Media hosting
- Data lakes & big data analytics
- Software delivery
- Static website

### Amazon S3 - Buckets
- Amazon S3 allows people to store objects (files) in “buckets” (directories)
- Buckets must have a **globally unique name (across all regions all accounts)**
- Buckets are defined at the region level
- S3 looks like a global service but buckets are created in a region
- Naming convention
    - No uppercase, No underscore
    - 3-63 characters long
    - Not an IP
    - Must start with lowercase letter or number
    - Must NOT start with the prefix **xn--**
    - Must NOT end with the suffix **-s3alias**

### Amazon S3 - Objects
- Objects (files) have a Key
- The **key** is the FULL path:
    - s3://my-bucket/**my_file.txt**
    - s3://my-bucket/**my_folder1/another_folder/my_file.txt**
- The key is composed of **prefix** + *object name*
    - s3://my-bucket/**my_folder1/another_folder/***my_file.txt*
- There’s no concept of “directories” within buckets (although the UI will trick you to think otherwise)
- Just keys with very long names that contain slashes (“/”)

### Amazon S3 – Objects (cont.)
- Object values are the content of the body:
    - Max. Object Size is 5TB (5000GB)
    - If uploading more than 5GB, must use “multi-part upload”
- Metadata (list of text key / value pairs – system or user metadata)
- Tags (Unicode key / value pair – up to 10) – useful for security / lifecycle
- Version ID (if versioning is enabled)

### Amazon S3 – Security
- **User-Based**
    - **IAM Policies** – which API calls should be allowed for a specific user from IAM
- **Resource-Based**
    - **Bucket Policies** – bucket wide rules from the S3 console - allows cross account
    - **Object Access Control List (ACL)** – finer grain (can be disabled)
    - **Bucket Access Control List (ACL)** – less common (can be disabled)
- **Note**: an IAM principal can access an S3 object if
    - The user IAM permissions ALLOW it OR the resource policy ALLOWS it
    - AND there’s no explicit DENY
- **Encryption**: encrypt objects in Amazon S3 using encryption keys

### S3 Bucket Policies
- JSON based policies
    - Resources: buckets and objects
    - Effect: Allow / Deny
    - Actions: Set of API to Allow or Deny
    - Principal: The account or user to apply the policy to
- Use S3 bucket for policy to:
    - Grant public access to the bucket
    - Force objects to be encrypted at upload
    - Grant access to another account (Cross Account)

### Bucket settings for Block Public Access
- **These settings were created to prevent company data leaks**
- If you know your bucket should never be public, leave these on
- Can be set at the account level

### S3 Bucket Policies
- Use S3 bucket for policy to:
    - Grant public access to the bucket
    - Force objects to be encrypted at upload
    - **Grant access to another account (Cross Account)**
- Optional Conditions on:
    - Public IP or Elastic IP (not on Private IP)
    - Source VPC or Source VPC Endpoint – only works with VPC Endpoints
    - CloudFront Origin Identity
    - MFA
- Examples here: https://docs.aws.amazon.com/AmazonS3/latest/dev/examplebucket-policies.html

### Bucket Policies – Advanced Examples
- Restrict access to only principals from AWS accounts inside an AWS Organization using **aws:PrincipalOrgID** condition key
- Prevent uploads of unencrypted objects to an S3 bucket using **s3:x-amz-server-sideencryption** condition key

### Amazon S3 – Static Website Hosting
- S3 can host static websites and have them accessible on the Internet
- The website URL will be (depending on the region)
    - http://bucket-name.s3-website-aws-region.amazonaws.com
    OR
    - http://bucket-name.s3-website.aws-region.amazonaws.com
- If you get a **403 Forbidden** error, make sure the bucket policy allows public reads!

### Amazon S3 - Versioning
- You can version your files in Amazon S3
- It is enabled at the **bucket level**
- Same key overwrite will change the “version”: 1, 2, 3….
- It is best practice to version your buckets
    - Protect against unintended deletes (ability to restore a version)
    - Easy roll back to previous version
- Notes:
    - Any file that is not versioned prior to enabling versioning will have version “null”
    - Suspending versioning does not delete the previous versions

### Amazon S3 – Replication (CRR & SRR)
- **Must enable Versioning** in source and destination buckets
- **Cross-Region Replication (CRR)**
- **Same-Region Replication (SRR)**
- Buckets can be in different AWS accounts
- Copying is asynchronous
- Must give proper IAM permissions to S3
- Use cases:
    - **CRR** – compliance, lower latency access, replication across accounts
    - **SRR** – log aggregation, live replication between production and test accounts

### Amazon S3 – Replication (Notes)
- After you enable Replication, only new objects are replicated
- Optionally, you can replicate existing objects using **S3 Batch Replication**
    - Replicates existing objects and objects that failed replication
- For DELETE operations
    - **Can replicate delete markers** from source to target (optional setting)
    - Deletions with a version ID are not replicated (to avoid malicious deletes)
- **There is no “chaining” of replication**
    - If bucket 1 has replication into bucket 2, which has replication into bucket 3
    - Then objects created in bucket 1 are not replicated to bucket 3

### S3 Storage Classes ***
- Amazon S3 Standard - General Purpose
- Amazon S3 Standard-Infrequent Access (IA)
- Amazon S3 One Zone-Infrequent Access
- Amazon S3 Glacier Instant Retrieval
- Amazon S3 Glacier Flexible Retrieval
- Amazon S3 Glacier Deep Archive
- Amazon S3 Intelligent Tiering

- Can move between classes manually or using S3 Lifecycle configurations

### S3 Durability and Availability
- Durability:
    - High durability (99.999999999%, 11 9’s) of objects across multiple AZ
    - If you store 10,000,000 objects with Amazon S3, you can on average expect to incur a loss of a single object once every 10,000 years
    - Same for all storage classes
- Availability:
    - Measures how readily available a service is
    - Varies depending on storage class
    - Example: S3 standard has 99.99% availability = not available 53 minutes a year

### S3 Standard – General Purpose
- 99.99% Availability
- Used for frequently accessed data
- Low latency and high throughput
- Sustain 2 concurrent facility failures

- Use Cases: Big Data analytics, mobile & gaming applications, content distribution…

### S3 Storage Classes – Infrequent Access
- For data that is less frequently accessed, but requires rapid access when needed
- Lower cost than S3 Standard

- **Amazon S3 Standard-Infrequent Access (S3 Standard-IA)**
    - 99.9% Availability
    - Use cases: Disaster Recovery, backups
- **Amazon S3 One Zone-Infrequent Access (S3 One Zone-IA)**
    - High durability (99.999999999%) in a single AZ; data lost when AZ is destroyed
    - 99.5% Availability
    - Use Cases: Storing secondary backup copies of on-premises data, or data you can recreate

### Amazon S3 Glacier Storage Classes
- Low-cost object storage meant for archiving / backup
- Pricing: price for storage + object retrieval cost
- **Amazon S3 Glacier Instant Retrieval**
    - Millisecond retrieval, great for data accessed once a quarter
    - Minimum storage duration of 90 days
- **Amazon S3 Glacier Flexible Retrieval** (formerly Amazon S3 Glacier):
    - Expedited (1 to 5 minutes), Standard (3 to 5 hours), Bulk (5 to 12 hours) – free
    - Minimum storage duration of 90 days
- **Amazon S3 Glacier Deep Archive – for long term storage:**
    - Standard (12 hours), Bulk (48 hours)
    - Minimum storage duration of 180 days

### S3 Intelligent-Tiering
- Small monthly monitoring and auto-tiering fee
- Moves objects automatically between Access Tiers based on usage
- There are no retrieval charges in S3 Intelligent-Tiering

- Frequent Access tier (automatic): default tier
- Infrequent Access tier (automatic): objects not accessed for 30 days
- Archive Instant Access tier (automatic): objects not accessed for 90 days
- Archive Access tier (optional): configurable from 90 days to 700+ days
- Deep Archive Access tier (optional): config. from 180 days to 700+ days