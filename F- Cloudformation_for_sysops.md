### Infrastructure as Code ***(all of them)
- Currently, we have been doing a lot of manual work
- All this manual work will be very tough to reproduce:
    - In another region
    - in another AWS account
    - Within the same region if everything was deleted
- Wouldn’t it be great, if all our infrastructure was… code?
- That code would be deployed and create / update / delete our infrastructure

### What is CloudFormation
- CloudFormation is a declarative way of outlining your AWS Infrastructure, for any resources (most of them are supported).
- For example, within a CloudFormation template, you say:
    - I want a security group
    - I want two EC2 machines using this security group
    - I want two Elastic IPs for these EC2 machines
    - I want an S3 bucket
    - I want a load balancer (ELB) in front of these machines
- Then CloudFormation creates those for you, in the right order, with the exact configuration that you specify

### Benefits of AWS CloudFormation (1/2)
- Infrastructure as code
    - No resources are manually created, which is excellent for control
    - The code can be version controlled for example using git
    - Changes to the infrastructure are reviewed through code REVIEW
- Cost
    - Each resources within the stack is staged with an identifier so you can easily see how much a stack costs you
    - You can estimate the costs of your resources using the CloudFormation template
    - Savings strategy: In Dev, you could automate deletion of templates at 5 PM and recreated at 8 AM, safely

### Benefits of AWS CloudFormation (2/2)
- Productivity
    - Ability to destroy and re-create an infrastructure on the cloud on the fly
    - Automated generation of Diagram for your templates!
    - Declarative programming (no need to figure out ordering and orchestration)
- Separation of concern: create many stacks for many apps, and many layers. Ex:
    - VPC stacks
    - Network stacks
    - App stacks
- Don’t re-invent the wheel
    - Leverage existing templates on the web!
    - Leverage the documentation

### How CloudFormation Works
- Templates have to be uploaded in S3 and then referenced in CloudFormation
- To update a template, we can’t edit previous ones. We have to reupload a new version of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by CloudFormation.

### Deploying CloudFormation templates
- Manual way:
    - Editing templates in the CloudFormation Designer
    - Using the console to input parameters, etc
- Automated way:
    - Editing templates in a YAML file
    - Using the AWS CLI (Command Line Interface) to deploy the templates
    - Recommended way when you fully want to automate your flow

### CloudFormation Building Blocks
Templates components (one course section for each):
1. Resources: your AWS resources declared in the template (MANDATORY)
2. Parameters: the dynamic inputs for your template
3. Mappings: the static variables for your template
4. Outputs: References to what has been created
5. Conditionals: List of conditions to perform resource creation
6. Metadata

Templates helpers:
1. References
2. Functions

### Note:
This is an introduction to CloudFormation
- It can take over 3 hours to properly learn and master CloudFormation
- This section is meant so you get a good idea of how it works
- We’ll be slightly less hands-on than in other sections
- We’ll learn everything we need to answer questions for the exam
- The exam does not require you to actually write CloudFormation
- The exam expects you to understand how to read CloudFormation

### Introductory Example
- We’re going to create a simple EC2 instance.
- Then we’re going to create to add an Elastic IP to it
- And we’re going to add two security groups to it
- For now, forget about the code syntax.
- We’ll look at the structure of the files later on

- We’ll see how in no-time, we are able to get started with CloudFormation!

### What are resources?
- Resources are the core of your CloudFormation template (MANDATORY), and CF cannot work without resources
- They represent the different AWS Components that will be created and configured
- Resources are declared and can reference each other
- AWS figures out creation, updates and deletes of resources for us
- There are over 224 types of resources (!)
- Resource types identifiers are of the form:
    `AWS::aws-product-name::data-type-name`

### How do I find
resources documentation?
- I can’t teach you all of the 224 resources, but I can teach you how to learn how to use them.
- All the resources can be found here:
http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html
- Then, we just read the docs J
- Example here (for an EC2 instance):
http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html

### Analysis of CloudFormation Template
- Going back to the example of the introductory section, let’s learn why it was written this way.
- Relevant documentation can be found here:
- http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/awsproperties-ec2-instance.html
- http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/awsproperties-ec2-security-group.html
- http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/awsproperties-ec2-eip.html

### FAQ for resources
- Can I create a dynamic amount of resources?
    - No, you can’t. Everything in the CloudFormation template has to be declared. You can’t perform code generation there
- Is every AWS Service supported?
    - Almost. Only a select few niches are not there yet
    - You can work around that using AWS Lambda Custom Resources

### What are parameters?
- Parameters are a way to provide inputs to your AWS CloudFormation template
- They’re important to know about if:
    - You want to reuse your templates across the company
    - Some inputs can not be determined ahead of time
- Parameters are extremely powerful, controlled, and can prevent errors from happening in your templates thanks to types.

### When should you use a parameter?
- Ask yourself this:
    - Is this CloudFormation resource configuration likely to change in the future?
    - If so, make it a parameter.
- You won’t have to re-upload a template to change its content

### Parameters Settings
Parameters can be controlled by all these settings:
- Type:
    - String
    - Number
    - CommaDelimitedList
    - List<Type>
    - AWS Parameter (to help catch invalid values – match against existing values in the AWS Account)
- Description
- Constraints
- ConstraintDescription (String)
- Min/MaxLength
- Min/MaxValue
- Defaults
- AllowedValues (array)
- AllowedPattern (regexp)
- NoEcho (Boolean)

### How to Reference a Parameter
- The `Fn::Ref` function can be leveraged to reference parameters
- Parameters can be used anywhere in a template.
- The shorthand for this in YAML is `!Ref`
- The function can also reference other elements within the template

### Concept: Pseudo Parameters
- AWS offers us pseudo parameters in any CloudFormation template.
- These can be used at any time and are enabled by default

### What are mappings?
- Mappings are fixed variables within your CloudFormation Template. they have to be hard coded
- They’re very handy to differentiate between different environments (dev vs prod), regions (AWS regions), AMI types, etc
- All the values are hardcoded within the template

### When would you use mappings vs parameters ?
- Mappings are great when you know in advance all the values that can be taken and that they can be deduced from variables such as
    - Region
    - Availability Zone
    - AWS Account
    - Environment (dev vs prod)
    - Etc…
- They allow safer control over the template.
- Use parameters when the values are really user specific

### Fn::FindInMap Accessing Mapping Values
- We use **Fn::FindInMap** to return a named value from a specific key
- **!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]**

### What are outputs? ***
- The Outputs section declares optional outputs values that we can import into other stacks (if you export them first)!
- You can also view the outputs in the AWS Console or in using the AWS CLI
- They’re very useful for example if you define a network CloudFormation, and output the variables such as VPC ID and your Subnet IDs
- It’s the best way to perform some collaboration cross stack, as you let expert handle their own part of the stack
- You can’t delete a CloudFormation Stack if its outputs are being referenced by another CloudFormation stack

### Outputs Example
- Creating a SSH Security Group as part of one template
- We create an output that references that security group

### Cross Stack Reference ***
- We then create a second template that leverages that security group
- For this, we use the **Fn::ImportValue function**
- You can’t delete the underlying stack until all the references are deleted too.

### What are conditions used for?
- Conditions are used to control the creation of resources or outputs based on a condition.
- Conditions can be whatever you want them to be, but common ones are:
    - Environment (dev / test / prod)
    - AWS Region
    - Any parameter value
- Each condition can reference another condition, parameter value or mapping

### How to define a condition?
- The logical ID is for you to choose. It’s how you name condition
- The intrinsic function (logical) can be any of the following:
- Fn::And
- Fn::Equals
- Fn::If
- Fn::Not
- Fn::Or

### Using a Condition
- Conditions can be applied to resources / outputs / etc…

### CloudFormation
Must Know Intrinsic Functions
- Ref
- Fn::GetAtt
- Fn::FindInMap
- Fn::ImportValue
- Fn::Join
- Fn::Sub
- Condition Functions (Fn::If, Fn::Not, Fn::Equals, etc…)

### Fn::Ref
- The Fn::Ref function can be leveraged to reference
    - Parameters => returns the value of the parameter
    - Resources => returns the physical ID of the underlying resource (ex: EC2 ID)
- The shorthand for this in YAML is **!Ref**

### Fn::GetAtt
- Attributes are attached to any resources you create
- To know the attributes of your resources, the best place to look at is the documentation.
- For example: the AZ of an EC2 machine!

### Fn::FindInMap Accessing Mapping Values
- We use **Fn::FindInMap** to return a named value from a specific key
- **!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]**

### Fn::ImportValue
- Import values that are exported in other templates
- For this, we use the **Fn::ImportValue** function

### Fn::Join
- Join values with a delimiter **!Join [delimiter, [comma-delimited list of values]]**
- This creates “a:b:c” **!Join [":", [a, b, c]]**

### Function Fn::Sub ***
- **Fn::Sub**, or **!Sub** as a shorthand, is used to substitute variables from a text. It’s a very handy function that will allow you to fully customize your templates.
- For example, you can combine **Fn::Sub** with References or AWS Pseudo variables!
- String must contain **${VariableName}** and will substitute them

### Condition Functions
- The logical ID is for you to choose. It’s how you name condition
- The intrinsic function (logical) can be any of the following:
- Fn::And
- Fn::Equals
- Fn::If
- Fn::Not
- Fn::Or

### User Data in EC2 for CloudFormation
- We can have user data at EC2 instance launch through the console
- We can also include it in CloudFormation

- **The important thing to pass is the entire script through the function Fn::Base64** the function will convert the script in base 64
- Good to know: user data script log is in **/var/log/cloud-init-output.log**

- Let’s see how to do this in CloudFormation

### cfn-init
- AWS::CloudFormation::Init must be in the Metadata of a resource
- With the cfn-init script, it helps make complex EC2 configurations readable
- The EC2 instance will query the CloudFormation service to get init data
- Logs go to **/var/log/cfn-init.log**
- Let’s see how it works through a sample CloudFormation

### cfn-signal & wait conditions
- We still don’t know how to tell CloudFormation that the EC2 instance got properly configured after a cfn-init
- For this, we can use the **cfn-signal** script!
    - We run cfn-signal right after cfn-init
    - Tell CloudFormation service to keep on going or fail
- We need to define **WaitCondition**:
    - Block the template until it receives a signal from cfnsignal
    - We attach a **CreationPolicy** (also works on EC2, ASG)

### Wait Condition Didn't Receive the Required Number of Signals from an Amazon EC2 Instance ***
- Ensure that the AMI you're using has the AWS CloudFormation helper scripts installed. If the AMI doesn't include the helper scripts, you can also download them to your instance.
- Verify that the cfn-init & cfn-signal command was successfully run on the instance. You can view logs, such as /var/log/cloud-init.log or **/var/log/cfn-init.log**, to help you debug the instance launch.
- You can retrieve the logs by logging in to your instance, but you must disable rollback on failure or else AWS CloudFormation deletes the instance after your stack fails to create.
- Verify that the instance has a connection to the Internet. If the instance is in a VPC, the instance should be able to connect to the Internet through a NAT device if it's is in a private subnet or through an Internet gateway if it's in a public subnet.
- For example, run: `curl -I https://aws.amazon.com`

### CloudFormation Rollbacks ***
- Stack Creation Fails:
    - Default: everything rolls back (gets deleted). We can look at the log
    - Option to disable rollback and troubleshoot what happened
- Stack Update Fails:
    - The stack automatically rolls back to the previous known working state
    - Ability to see in the log what happened and error messages

### Stack creation failures
- If a CloudFormation stack creation fails, you will get the status `ROLLBACK_COMPLETE`
- This means:
    - 1. CloudFormation tried to create some resources
    - 2. One resource creation failed
    - 3. CloudFormation rolled back the resources (ROLLBACK, DO_NOTHING)
    - 4. The stack is in failed created ROLLBACK_COMPLETE state
- To resolve the error, there’s only one way: Delete the failed stack and create a new stack
- You can’t update, validate or change-set on a create failed stack

### Nested stacks ***
- Nested stacks are stacks as part of other stacks
- They allow you to isolate repeated patterns / common components in separate stacks and call them from other stacks
- Example:
    - Load Balancer configuration that is re-used
    - Security Group that is re-used
- Nested stacks are considered best practice
- To update a nested stack, always update the parent (root stack)

### ChangeSets
- When you update a stack, you need to know what changes before it happens for greater confidence
- ChangeSets won’t say if the update will be successful

### CloudFormation Drift
- CloudFormation allows you to create infrastructure
- But it doesn’t protect you against manual configuration changes
- How do we know if our resources have `drifted`?
- We can use CloudFormation drift!
- Not all resources are supported yet: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift-resource-list.html

### Retaining Data on Deletes
- You can put a DeletionPolicy on any resource to control what happens when the CloudFormation template is deleted
- **DeletionPolicy=Retain**:
    - Specify on resources to preserve / backup in case of CloudFormation deletes
    - To keep a resource, specify Retain (works for any resource / nested stack)
- **DeletionPolicy=Snapshot**:
    - EBS Volume, ElastiCache Cluster, ElastiCache ReplicationGroup
    - RDS DBInstance, RDS DBCluster, Redshift Cluster
- **DeletePolicy=Delete (default behavior)**:
    - Note: for **AWS::RDS::DBCluster** resources, the default policy is Snapshot
    - Note: to delete an S3 bucket, you need to first empty the bucket of its content

### Termination Protection on Stacks
- To prevent accidental deletes of CloudFormation templates, use TerminationProtection

- Let’s see this quickly!

### CloudFormation StackSets
- Create, update, or delete stacks across multiple accounts and regions with a single operation
- Administrator account to create StackSets
- Trusted accounts to create, update, delete stack instances from StackSets
- When you update a stack set, all associated stack instances are updated throughout all accounts and regions.
- Ability to set a maximum concurrent actions on targets (# or %)
- Ability to set failure tolerance (# or %)

### Continue Rolling Back an Update
- A stack goes into the UPDATE_ROLLBACK_FAILED state when CloudFormation can’t roll back all changes during an update
- A resource can’t return to its original state, causing the rollback to fail
- Example: roll back to an old database instance that was deleted outside CloudFormation
- Solutions:
    - Fix the errors manually outside of CloudFormation and then continue update rollback the stack
    - Skip the resources that can’t rollback successfully (CloudFormation will mark the failed resources as UPDATE_COMPLETE)
- You can’t update a stack in this state
- For nested stacks, rolling back the parent stack will attempt to roll back all the child stacks as well 