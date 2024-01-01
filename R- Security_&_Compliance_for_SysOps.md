### AWS Shared Responsibility Model
- AWS responsibility - Security **of** the Cloud
    - Protecting infrastructure (hardware, software, facilities, and networking) that runs all the AWS services
    - Managed services like S3, DynamoDB, RDS, etc.
- Customer responsibility - Security **in** the Cloud
    - For EC2 instance, customer is responsible for management of the guest OS (including security patches and updates), firewall & network configuration, IAM
    - Encrypting application data
- Shared controls:
    - Patch Management, Configuration Management, Awareness & Training

### Example, for RDS
- AWS responsibility:
    - Manage the underlying EC2 instance, disable SSH access
    - Automated DB patching
    - Automated OS patching
    - Audit the underlying instance and disks & guarantee it functions
- Your responsibility:
    - Check the ports / IP / security group inbound rules in DB’s SG
    - In-database user creation and permissions
    - Creating a database with or without public access
    - Ensure parameter groups or DB is configured to only allow SSL connections
    - Database encryption setting

### Example, for S3
- AWS responsibility:
    - Guarantee you get unlimited storage
    - Guarantee you get encryption
    - Ensure separation of the data between different customers
    - Ensure AWS employees can’t access your data
- Your responsibility:
    - Bucket configuration
    - Bucket policy / public setting
    - IAM user and roles
    - Enabling encryption

### Shared Responsibility Model diagram ***
https://aws.amazon.com/compliance/shared-responsibility-model/

### What’s a DDOS* Attack?
*Distributed Denial-of-Service

### DDoS Protection on AWS
- **AWS Shield Standard:** protects against DDoS attack for your website and applications, for all customers at no additional costs
- **AWS Shield Advanced:** 24/7 premium DDoS protection
- **AWS WAF:** Filter specific requests based on rules
- **CloudFront and Route 53:**
    - Availability protection using global edge network
    - Combined with AWS Shield, provides attack mitigation at the edge

- Be ready to scale – leverage AWS Auto Scaling

### Sample Reference Architecture
https://d1.awsstatic.com/whitepapers/Security/DDoS_White_Paper.pdf

### AWS Shield
- **AWS Shield Standard:**
    - Free service that is activated for every AWS customer
    - It is activated by default for every customer
    - Provides protection from attacks such as SYN/UDP Floods, Reflection attacks and other layer 3/layer 4 attacks
- **AWS Shield Advanced:**
    - Optional DDoS mitigation service ($3,000 per month per organization)
    - Protect against more sophisticated attack on Amazon EC2, Elastic Load Balancing (ELB), Amazon CloudFront, AWS Global Accelerator, and Route 53
    - 24/7 access to AWS DDoS response team (DRP)
    - Protect against higher fees during usage spikes due to DDoS

### AWS WAF – Web Application Firewall
- Protects your web applications from common web exploits (Layer 7)
- **Layer 7 is HTTP** (vs Layer 4 is TCP)
- Deploy on **Application Load Balancer, API Gateway, CloudFront**
- Define Web ACL (Web Access Control List):
    - Rules can include: **IP addresses**, HTTP headers, HTTP body, or URI strings
    - Protects from common attack - **SQL injection** and **Cross-Site Scripting (XSS)**
    - Size constraints, **geo-match (block countries)**
    - **Rate-based rules** (to count occurrences of events) – **for DDoS protection**

### Penetration Testing on AWS Cloud
- Penetration Testing is when you attack your own infrastructure to test your own security
- AWS customers are welcome to carry out security assessments or penetration tests against their AWS infrastructure **without prior approval for 8 services:**
    - Amazon EC2 instances, NAT Gateways, and Elastic Load Balancers
    - Amazon RDS
    - Amazon CloudFront
    - Amazon Aurora
    - Amazon API Gateways
    - AWS Lambda and Lambda Edge functions
    - Amazon Lightsail resources
    - Amazon Elastic Beanstalk environments
- List can increase over time (you won’t be tested on that at the exam)

### Penetration Testing on your AWS Cloud
- **Prohibited Activities**
    - DNS zone walking via Amazon Route 53 Hosted Zones
    - Denial of Service (DoS), Distributed Denial of Service (DDoS), Simulated DoS, Simulated DDoS
    - Port flooding
    - Protocol flooding
    - Request flooding (login request flooding, API request flooding)
- For any other simulated events, contact aws-security-simulatedevent@amazon.com
- Read more: https://aws.amazon.com/security/penetration-testing/

### Amazon Inspector
Amazon Inspector is a service that allows you ro run security assessments
- **Automated Security Assessments**

- **For EC2 instances**
    - Leveraging the **AWS System Manager (SSM) agent**
    - Analyze against **unintended network accessibility**
    - Analyze the **running OS** against **known vulnerabilities**
- **For Container Images push to Amazon ECR**
    - Assessment of Container Images as they are pushed
- **For Lambda Functions**
    - Identifies software vulnerabilities in function code and package dependencies
    - Assessment of functions as they are deployed

- Reporting & integration with AWS Security Hub
- Send findings to Amazon Event Bridge

### What does Amazon Inspector evaluate?
- **Remember: only for EC2 instances, Container Images & Lambda functions**

- Continuous scanning of the infrastructure, only when needed

- Package vulnerabilities (EC2, ECR & Lambda) – database of CVE
- Network reachability (EC2)

- Every time it runs, A risk score is associated with all vulnerabilities for prioritization

### Logging in AWS for security and compliance
- To help compliance requirements, AWS provides many service-specific security and audit logs
- Service Logs include:
    - CloudTrail trails - trace all API calls
    - Config Rules - for config & compliance over time
    - CloudWatch Logs - for full data retention
    - VPC Flow Logs - IP traffic within your VPC
    - ELB Access Logs - metadata of requests made to your load balancers
    - CloudFront Logs - web distribution access logs
    - WAF Logs - full logging of all requests analyzed by the service
- **Logs can be analyzed using AWS Athena if they’re stored in S3**
- **You should encrypt logs in S3, control access using IAM & Bucket Policies, MFA**
- **Move Logs to Glacier for cost savings**
- Read whitepaper if interested at: https://d0.awsstatic.com/whitepapers/compliance/AWS_Security_at_Scale_Logging_in_AWS_Whitepaper.pdf

### Amazon GuardDuty
- Intelligent Threat discovery to protect your AWS Account
- Uses Machine Learning algorithms, anomaly detection, 3rd party data to find the threads
- One click to enable (30 days trial), no need to install software
- Input data includes:
    - **CloudTrail Events Logs** – unusual API calls, unauthorized deployments
        - **CloudTrail Management Events** – create VPC subnet, create trail, …
        - **CloudTrail S3 Data Events** – get object, list objects, delete object, …
    - **VPC Flow Logs** – unusual internal traffic, unusual IP address
    - **DNS Logs** – compromised EC2 instances sending encoded data within DNS queries
    - **Optional Feature** – EKS Audit Logs, RDS & Aurora, EBS, Lambda, S3 Data Events…
- Can setup **EventBridge rules** to be notified in case of findings
- EventBridge rules can target AWS Lambda or SNS
- **Can protect against CryptoCurrency attacks (has a dedicated “finding” for it)**

### AWS Macie
- Amazon Macie is a fully managed data security and data privacy service that uses **machine learning and pattern matching to discover and protect your sensitive data in AWS.**
- Macie helps identify and alert you to **sensitive data, such as personally identifiable information (PII)**

### Trusted Advisor
- No need to install anything – high level AWS account assessment
- Analyze your AWS accounts and provides recommendation on 5 categories
- **Cost optimization**
- **Performance**
- **Security**
- **Fault tolerance**
- **Service limits**

### Trusted Advisor – Support Plans
**7 CORE CHECKS - Basic & Developer Support plan**
- S3 Bucket Permissions
- Security Groups – Specific Ports Unrestricted
- IAM Use (one IAM user minimum)
- MFA on Root Account
- EBS Public Snapshots
- RDS Public Snapshots
- Service Limits
**FULL CHECKS - Business & Enterprise Support plan**
- Full Checks available on the 5 categories
- Ability to set CloudWatch alarms when reaching limits
- **Programmatic Access using AWS Support API**

### Why encryption?
**Encryption in flight (TLS / SSL)**
- Data is encrypted before sending and decrypted after receiving
- TLS certificates help with encryption (HTTPS)
- Encryption in flight ensures no MITM (man in the middle attack) can happen

### Why encryption?
**Server-side encryption at rest**
- Data is encrypted after being received by the server
- Data is decrypted before being sent
- It is stored in an encrypted form thanks to a key (usually a data key)
- The encryption / decryption keys must be managed somewhere, and the server must have access to it

### Why encryption?
**Client-side encryption**
- Data is encrypted by the client and never decrypted by the server
- Data will be decrypted by a receiving client
- The server should not be able to decrypt the data
- Could leverage Envelope Encryption

### AWS KMS (Key Management Service)
- Anytime you hear “encryption” for an AWS service, it’s most likely KMS
- AWS manages encryption keys for us
- Fully integrated with IAM for authorization
- Easy way to control access to your data
- Able to audit KMS Key usage using CloudTrail
- Seamlessly integrated into most AWS services (EBS, S3, RDS, SSM…)
- **Never ever store your secrets in plaintext, especially in your code!**
    - KMS Key Encryption also available through API calls (SDK, CLI)
    - Encrypted secrets can be stored in the code / environment variables

### KMS Keys Types
- **KMS Keys is the new name of KMS Customer Master Key**
- **Symmetric (AES-256 keys)**
    - Single encryption key that is used to Encrypt and Decrypt
    - AWS services that are integrated with KMS use Symmetric CMKs
    - You never get access to the KMS Key unencrypted (must call KMS API to use)
- **Asymmetric (RSA & ECC key pairs)**
    - Public (Encrypt) and Private Key (Decrypt) pair
    - Used for Encrypt/Decrypt, or Sign/Verify operations
    - The public key is downloadable, but you can’t access the Private Key unencrypted
    - Use case: encryption outside of AWS by users who can’t call the KMS API

### AWS KMS (Key Management Service)
- Types of KMS Keys:
    - AWS Owned Keys (free): SSE-S3, SSE-SQS, SSE-DDB (default key)
    - AWS Managed Key: **free** (aws/service-name, example: aws/rds or aws/ebs)
    - Customer managed keys created in KMS: **$1 / month**
    - Customer managed keys imported (must be symmetric key): **$1 / month**
    - + pay for API call to KMS ($0.03 / 10000 calls)

- Automatic Key rotation:
    - AWS-managed KMS Key: automatic every 1 year
    - Customer-managed KMS Key: (must be enabled) automatic every 1 year
    - Imported KMS Key: only manual rotation possible using alias

### Copying Snapshots across regions

### KMS Key Policies
- Control access to KMS keys, “similar” to S3 bucket policies
- Difference: you cannot control access without KMS key policies

Type of KMS key policies
- **Default KMS Key Policy:**
    - Created if you don’t provide a specific KMS Key Policy
    - Complete access to the key to the root user = entire AWS account
- **Custom KMS Key Policy:**
    - Define users, roles that can access the KMS key
    - Define who can administer the key
    - Useful for cross-account access of your KMS key

### Copying Snapshots across accounts
1. Create a Snapshot, encrypted with your own KMS Key (Customer Managed Key)
2. **Attach a KMS Key Policy to authorize cross-account access**
3. Share the encrypted snapshot with the target account
4. (in target) Create a copy of the Snapshot, encrypt it with a CMK in your account
5. Create a volume from the snapshot

### KMS Automatic Key Rotation
- **For Customer-managed CMK** (not AWS managed CMK)
- If enabled: automatic key rotation happens **every 1 year**
- Previous key is kept active so you can decrypt old data
- New Key has the same CMK ID (only the backing key is changed)

### KMS Manual Key Rotation
- **When you want to rotate key every 90 days, 180 days, etc…**
- New Key has a different CMK ID because you create it manually
- Keep the previous key active so you can decrypt old data
- Better to use aliases in this case (to hide the change of key for the application)
- Good solution to rotate CMK that are not eligible for automatic rotation (like asymmetric CMK)

### KMS Alias Updating
- Better to use aliases in this case (to hide the change of key for the application)

### Changing The KMS Key For An Encrypted EBS Volume
- You can’t change the encryption keys used by an EBS volume
- Create an EBS snapshot and create a new EBS volume and specify the new KMS key

### Sharing KMS Encrypted RDS DB Snapshots
- You can share RDS DB snapshots encrypted with KMS CMK with other accounts, **but must first share the KMS CMK with the target account using Key Policy**

### KMS Key Deletion Considerations
- Schedule CMK for deletion with a waiting period of 7 to 30 days
    - CMK’s status is “Pending deletion” during the waiting period
- During the CMK’s deletion waiting period:
    - **The CMK can’t be used for cryptographic operations** (e.g., can’t decrypt KMSencrypted objects in S3 – SSE-KMS)
    - The key is not rotated even if planned

- You can cancel the key deletion during the waiting period

- Consider disabling your key instead of deleting it if you’re not sure!

### KMS Key Deletion – CloudWatch Alarm
- Use CloudTrail, CloudWatch Logs, CloudWatch Alarms and SNS to be notified when someone tries to use a CMK that’s **”Pending deletion”** in a cryptographic operation (Encrypt, Decrypt, …)

### CloudHSM
- KMS => AWS manages the software for encryption
- CloudHSM => AWS provisions encryption **hardware**
- Dedicated Hardware (HSM = Hardware Security Module)
- You manage your own encryption keys entirely (not AWS)
- HSM device is tamper resistant, FIPS 140-2 Level 3 compliance
- Supports both symmetric and **asymmetric** encryption (SSL/TLS keys)
- No free tier available
- Must use the CloudHSM Client Software
- Redshift supports CloudHSM for database encryption and key management
- **Good option to use with SSE-C encryption**

### CloudHSM Diagram
**IAM permissions:**
- CRUD an HSM Cluster
**CloudHSM Software:**
- Manage the Keys
- Manage the Users

### CloudHSM – High Availability
- CloudHSM clusters are spread across Multi AZ (HA)
- Great for availability and durability

### CloudHSM – Integration with AWS Services
- Through integration with AWS KMS
- Configure KMS Custom Key Store with CloudHSM
- Example: EBS, S3, RDS …

### CloudHSM vs. KMS

### AWS Artifact (not really a service)
- **Portal that provides customers with on-demand access to AWS compliance documentation and AWS agreements**
- **Artifact Reports** - Allows you to download AWS security and compliance documents from third-party auditors, like AWS ISO certifications, Payment Card Industry (PCI), and System and Organization Control (SOC) reports
- **Artifact Agreements** - Allows you to review, accept, and track the status of AWS agreements such as the Business Associate Addendum (BAA) or the Health Insurance Portability and Accountability Act (HIPAA) for an individual account or in your organization

- Can be used to **support internal audit or compliance**

### AWS Certificate Manager (ACM)
- Let’s you easily provision, manage, and deploy **SSL/TLS Certificates**
- Used to provide in-flight encryption for websites (HTTPS)
- Supports both public and private TLS certificates
- Free of charge for public TLS certificates
- Automatic TLS certificate renewal
- Integrations with (load TLS certificates on)
    - Elastic Load Balancers
    - CloudFront Distributions
    - APIs on API Gateway

### AWS Secrets Manager
- Newer service, meant for storing secrets
- Capability to force **rotation of secrets** every X days
- Automate generation of secrets on rotation (uses Lambda)
- Integration with **Amazon RDS**(MySQL, PostgreSQL, Aurora)
- Secrets are encrypted using KMS

- Mostly meant for RDS integration

### AWS Secrets Manager – Multi-Region Secrets
- Replicate Secrets across multiple AWS Regions
- Secrets Manager keeps read replicas in sync with the primary Secret
- Ability to promote a read replica Secret to a standalone Secret
- Use cases: multi-region apps, disaster recovery strategies, multi-region DB…

### Secrets Manager – Monitoring
- CloudTrail captures API calls to the Secrets Manager API
- CloudTrail captures other related events that might have a security or compliance impact on your AWS account or might help you troubleshoot operational problems.
- **CloudTrail records these events as non-API service events.**
    - RotationStarted event
    - RotationSucceeded event
    - RotationFailed event
    - RotationAbandoned event – a manual change to a secret instead of automated rotation
    - StartSecretVersionDelete event
    - CancelSecretVersionDelete event
    - EndSecretVersionDelete event
- Combine with CloudWatch Logs and CloudWatch alarms for automation

### Secrets Manager – Troubleshooting Rotation

### SSM Parameter Store vs Secrets Manager
- **Secrets Manager is more expensive because of secret rotation($$$):**
    - Automatic rotation of secrets with AWS Lambda
    - Lambda function is provided for RDS, Redshift, DocumentDB
    - KMS encryption is mandatory
    - Can integration with CloudFormation
- **SSM Parameter Store is less expense and there is no secret rotation($):**
    - Simple API
    - No secret rotation (can enable rotation using Lambda triggered by EventBridge)
    - KMS encryption is optional
    - Can integration with CloudFormation
    - Can pull a Secrets Manager secret using the SSM Parameter Store API

### SSM Parameter Store vs. Secrets Manager Rotation

### SSM Parameter Store
- Secure storage for configuration and secrets
- Optional Seamless Encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations / secrets
- Configuration management using path & IAM
- Notifications with CloudWatch Events
- Integration with CloudFormation

### SSM Parameter Store Hierarchy
- /my-department/
    - my-app/
        - dev/
            - db-url
    - db-password
        - prod/
            - db-url
            - db-password
    - other-app/
- /other-department/
- /aws/reference/secretsmanager/secret_ID_in_Secrets_Manager
- /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2

### Standard and advanced parameter tiers

### Parameters Policies (for advanced parameters)
- Allow to assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as passwords
- Can assign multiple policies at a time

