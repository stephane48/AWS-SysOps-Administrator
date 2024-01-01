### AWS Health Dashboard - Service History
- Shows all regions, all services health
- Shows historical information for each day
- Has an RSS feed you can subscribe to
- Previously called AWS Service Health Dashboard

### AWS Health Dashboard – Your Account
- Previously called AWS Personal Health Dashboard (PHD)
- AWS Account Health Dashboard provides **alerts and remediation guidance** when AWS is experiencing events that may impact you.
- While the Service Health Dashboard displays the general status of AWS services, Account Health Dashboard gives you a **personalized view into the performance and availability of the AWS services underlying your AWS resources.**
- The dashboard displays **relevant and timely information** to help you manage events in progress and provides **proactive notification** to help you plan for **scheduled activities.**
- **Can aggregate data from an entire AWS Organization**

### AWS Health Dashboard – Your Account
- Global service
- Shows how AWS outages directly impact you & your AWS resources
- Alert, remediation, proactive, scheduled activities

### Health Event Notifications
- Use EventBridge to react to changes for AWS Health events in your AWS account
- Example: receive email notifications when EC2 instances in your AWS account are scheduled for updates
- This is possible for Account events (resources that are affected in your account) and Public Events (Regional availability of a service)
- Use cases: send notifications, capture event information, take corrective action...

### AWS Health Dashboard – Examples

### AWS Organizations
- Global service
- Allows to manage multiple AWS accounts
- The main account is the management account
- Other accounts are member accounts
- Member accounts can only be part of one organization
- Consolidated Billing across all accounts - single payment method
- Pricing benefits from aggregated usage (volume discount for EC2, S3…)
- **Shared reserved instances and Savings Plans discounts across accounts**
- API is available to automate AWS account creation

### Organizational Units (OU) - Examples

### AWS Organizations
- **Advantages**
    - Multi Account vs One Account Multi VPC
    - Use tagging standards for billing purposes
    - Enable CloudTrail on all accounts, send logs to central S3 account
    - Send CloudWatch Logs to central logging account
    - Establish Cross Account Roles for Admin purposes
- **Security: Service Control Policies (SCP)**
    - IAM policies applied to OU or Accounts to restrict Users and Roles
    - They do not apply to the management account (full admin power)
    - Must have an explicit allow (does not allow anything by default – like IAM)

### SCP Hierarchy

### SCP Examples Blocklist and Allowlist strategies
Blocklist are lists of services that users are not allowed to use in an account
Allowlist are lists of services that users are allowed to use in an account

### AWS Organizations – Reserved Instances ***
- For billing purposes, the consolidated billing feature of AWS Organizations treats all the accounts in the organization as one account.
- This means that **all accounts** in the organization can receive the hourly cost benefit of Reserved Instances that are purchased **by any other account.**
- **The payer account (master account) of an organization** can turn off Reserved Instance (RI) discount and Savings Plans discount sharing for any accounts in that organization, including the payer account
- This means that RIs and Savings Plans discounts aren't shared between any accounts that have sharing turned off.
- To share an RI or Savings Plans discount with an account, both accounts must have sharing turned on.

### AWS Organizations – IAM Policies
- Use **aws:PrincipalOrgID** condition key in your resource-based policies to restrict access to IAM principals from accounts in an AWS Organization

### AWS Organizations – Tag Policies
- Helps you standardize tags across resources in an AWS Organization
- Ensure consistent tags, audit tagged resources, maintain proper resources categorization, …
- You define tag keys and their allowed values
- Helps with AWS Cost Allocation Tags and Attribute-based Access Control
- Prevent any non-compliant tagging operations on specified services and resources (has no effect on resources without tags)
- Generate a report that lists all tagged/non-compliant resources
- Use CloudWatch Events to monitor non-compliant tags

### AWS Control Tower
- Easy way to **set up and govern a secure and compliant multi-account AWS environment** based on best practices
- Benefits:
    - Automate the set up of your environment in a few clicks
    - Automate ongoing policy management using guardrails
    - Detect policy violations and remediate them
    - Monitor compliance through an interactive dashboard
- AWS Control Tower runs on top of AWS Organizations:
    - It automatically sets up AWS Organizations to organize accounts and implement SCPs (Service Control Policies)

### AWS Service Catalog ***
- Users that are new to AWS have too many options, and may create stacks that are not compliant / in line with the rest of the organization

- Some users just want a quick **self-service portal** to launch a set of **authorized products** pre-defined **by admins**

- Includes: virtual machines, databases, storage options, etc…

- Enter AWS Service Catalog!

### Service Catalog diagram

### AWS Service Catalog – Sharing Catalogs
- Share portfolios with individual AWS accounts or AWS Organizations
- Sharing options:
    - Share a reference of the portfolio, then import the shared portfolio in the recipient account (stays in-sync with the original portfolio)
    - Deploy a copy of the portfolio into the recipient account (must re-deploy any updates)
- Ability to add products from the imported portfolio to the local portfolio

### AWS Service Catalog – TagOptions Library
- Easily manage tags on provisioned products
    - **TagOption:**
    - Key-value pair managed in AWS Service Catalog
- Used to create an AWS Tag
- Can be associated with Portfolios and Products
- Use cases: proper resources tagging, defined allowed tags, …
- Can be shared with other AWS accounts and AWS Organizations

### AWS Billing Alarms
- **Billing data metric is stored in CloudWatch us-east-1**
- Billing data are for overall **worldwide** AWS costs
- It’s for actual cost, not for project costs

- Let’s create a billing alarm together!

### Cost Explorer ***
- It uses to Visualize, understand, and manage your AWS costs and usage over time
- Create custom reports that analyze cost and usage data.
- Analyze your data at a high level: total costs and usage across all accounts
- Or Monthly, hourly, resource level granularity
- Choose an optimal **Savings Plan** (to lower prices on your bill)
- **Forecast usage up to 12 months based on previous usage**

### Cost Explorer – Monthly Cost by AWS Service

### Cost Explorer– Hourly & Resource Level

### Cost Explorer – Savings Plan Alternative to Reserved Instances

### Cost Explorer – Forecast Usage ***

### AWS Budgets
- Create budget and **send alarms when costs exceeds the budget**
- 4 types of budgets: Usage, Cost, Reservation, Savings Plans
- For Reserved Instances (RI)
    - Track utilization
    - Supports EC2, ElastiCache, RDS, Redshift
- Up to 5 SNS notifications per budget
- Can filter by: Service, Linked Account, Tag, Purchase Option, Instance Type, Region, Availability Zone, API Operation, etc…
- Same options as AWS Cost Explorer!
- 2 budgets are free, then $0.02/day/budget

### Cost Allocation Tags
- Use **cost allocation tags** to track your AWS costs on a detailed level
- **AWS generated tags**
    - Automatically applied to the resource you create
    - Starts with Prefix aws: **(e.g. aws: createdBy)**
- **User-defined tags**
    - Defined by the user
    - Starts with Prefix **user**:

### Cost and Usage Reports
- Dive deeper into your AWS costs and usage
- The AWS Cost & Usage Report contains **the most comprehensive set of AWS cost and usage data available**
- Includes additional metadata about AWS services, pricing, and reservations **(e.g., Amazon EC2 Reserved Instances (RIs))**
- The AWS Cost & Usage Report lists AWS usage for each:
    - service category used by an account
    - in hourly or daily line items
    - any tags that you have activated for cost allocation purposes
- Can be configured for daily exports to S3
- Can be integrated with Athena, Redshift or QuickSight

### AWS Compute Optimizer
- **Reduce costs** and **improve performance by** recommending optimal AWS resources for your workloads
- Helps you choose optimal configurations and right-size your workloads (over/under provisioned)
- Uses Machine Learning to analyze your **resources’ configurations** and their **utilization CloudWatch metrics**
- Supported resources
    - EC2 instances
    - EC2 Auto Scaling Groups
    - EBS volumes
    - Lambda functions
- Lower your costs by up to 25%
- Recommendations can be exported to S3

### Cost and Usage Reports