### AWS Systems Manager Overview (Important for the exam)
- Helps you manage your EC2 and On-Premises systems at scale
- Get operational insights about the state of your infrastructure
- Easily detect problems
- Patching automation for enhanced compliance
- Works for both Windows and Linux OS
- Integrated with CloudWatch metrics / dashboards
- Integrated with AWS Config
- Free service

### How Systems Manager works
- We need to install the SSM agent onto the systems we control
- Installed by default on Amazon Linux 2 AMI & some Ubuntu AMI
- If an instance can’t be controlled with SSM, it’s probably an issue with the SSM agent!
- Make sure the EC2 instances have a proper IAM role to allow SSM actions

### AWS Tags
- You can add text key-value pairs called Tags to many AWS resources
- Commonly used in EC2
- Free naming, common tags are Name, Environment, Team …
- They’re used for
    - Resource grouping
    - Automation
    - Cost allocation
- Better to have too many tags than too few!

### Resource Groups
- Create, view or manage logical group of resources thanks to tags
- Allows creation of logical groups of resources such as
    - Applications
    - Different layers of an application stack
    - Production versus development environments
- Regional service
- Works with EC2, S3, DynamoDB, Lambda, etc…

### SSM – Documents
- Documents can be in JSON or YAML
- You define parameters
- You define actions
- Many documents already exist in AWS

### SSM – Run Command
- Execute a document (= script) or just run a command
- Run command across multiple instances (using resource groups)
- Rate Control / Error Control
- Integrated with IAM & CloudTrail
- No need for SSH
- Command Output can be shown in the Console, sent to S3 bucket or CloudWatch Logs
- Send notifications to SNS about command statues (In progress, Success, Failed, …)
- Can be invoked using EventBridge

### SSM - Automation
- Simplifies common maintenance and deployment tasks of EC2 instances and other AWS resources
- Example: restart instances, create an AMI, EBS snapshot
- Automation Runbook
    - SSM Documents of type Automation
    - Defines actions preformed on your EC2 instances or AWS resources
    - Pre-defined runbooks (AWS) or create custom runbooks
- Can be triggered
    - Manually using AWS Console, AWS CLI or SDK
    - By Amazon EventBridge
    - On a schedule using Maintenance Windows
    - By AWS Config for rules remediation

### SSM Parameter Store
- Secure storage for configuration and secrets
- Optional Seamless Encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations / secrets
- Security through IAM
- Notifications with Amazon EventBridge
- Integration with CloudFormation

### Parameters Policies (for advanced parameters)
- Allow to assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as passwords
- Can assign multiple policies at a time

Command to get parameter store value decrypted
`aws ssm get-parameters --names " /my-app/dev/db-url" " /my-app/dev/db-password" --with-decryption`
Command to get all the parameters store under a specific path
`aws ssm get-parameters-by-path --path " /my-app/dev/"`
Command to get all the parameters store under a folder
`aws ssm get-parameters-by-path --path " /my-app/" --recursive`

### SSM – Inventory
- Collect metadata from your managed instances (EC2/On-premises)
- Metadata includes installed software, OS drivers, configurations, installed updates, running services … It creates an inventory on what's happening on your managed instances
- View data in AWS Console or store in S3 and query and analyze using Athena (for serverless) and QuickSight (for dashboard)
- Specify metadata collection interval (minutes, hours, days)
- Query data from multiple AWS accounts and regions
- Create Custom Inventory for your custom metadata (e.g., rack location of each managed instance)

### SSM – State Manager
- Automate the process of keeping your managed instances (EC2/On-premises) in a state that you define
- Use cases: bootstrap instances with software, patch OS/software updates on a schedule …
- State Manager Association:
    - Defines the state that you want to maintain to your managed instances
    - Example: port 22 must be closed, antivirus must be installed …
    - Specify a schedule when this configuration is applied
- Uses SSM Documents to create an Association (e.g., SSM Document to configure CW Agent)

### SSM – Patch Manager
- Automates the process of patching managed instances
- OS updates, applications updates, security updates, …
- Supports both EC2 instances and on-premises servers
- Supports Linux, macOS, and Windows
- Patch on-demand or on a schedule using Maintenance Windows
- Scan instances and generate patch compliance report (missing patches)
- Patch compliance report can be sent to S3

### SSM – Patch Manager
- Patch Baseline
    - Defines which patches should and shouldn’t be installed on your instances
    - Ability to create custom Patch Baselines (specify approved/rejected patches)
    - Patches can be auto-approved within days of their release
    - By default, install only critical patches and patches related to security
- Patch Group
    - Associate a set of instances with a specific Patch Baseline
    - Example: create Patch Groups for different environments (dev, test, prod)
    - Instances should be defined with the tag key Patch Group
    - An instance can only be in one Patch Group
    - Patch Group can be registered with only one Patch Baseline

### SSM – Patch Manager Patch Baselines
- Pre-Defined Patch Baseline
    - Managed by AWS for different Operating Systems (can’t be modified)
    - AWS-RunPatchBaseline (SSM Document) – apply both operating system and application patches (Linux, macOS, Windows Server)
- Custom Patch Baseline
    - Create your own Patch Baseline and choose which patches to auto-approve
    - Operating System, allowed patches, rejected patches, …
    - Ability to specify custom and alternative patch repositories    

### SSM – Maintenance Windows
- Defines a schedule for when to perform actions on your instances
- Example: OS patching, updating drivers, installing software, …
- Maintenance Window contains
    - Schedule
    - Duration
    - Set of registered instances
    - Set of registered tasks    

### SSM – Session Manager
- Allows you to start a secure shell on your EC2 and on-premises servers
- Access through AWS Console, AWS CLI, or Session Manager SDK
- Does not need SSH access, bastion hosts, or SSH keys. This is its real power
- Supports Linux, macOS, and Windows
- Log connections to your instances and executed commands
- Session log data can be sent to S3 or CloudWatch Logs
- CloudTrail can intercept StartSession events 
- IAM Permissions
    - Control which users/groups can access Session Manager and which instances
    - Use tags to restrict access to only specific EC2 instances
    - Access SSM + write to S3 + write to CloudWatch
- Optionally, you can restrict commands a user can run in a session   