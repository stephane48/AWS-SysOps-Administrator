### Enhanced Networking (Important) ***
Enhanced Networking it is a way to get better performance for an EC2 instances:
- EC2 Enhanced Networking (SR-IOV)
    - Higher bandwidth, hipper packet per second, lower latency
    - Option 1: Elastic Network Adapter (ENA) up to 100 Gbps
    - Option 2: Intel 82599 VF up to 10 Gbps – LEGACY
    - Works for newer generation EC2 Instances
- Elastic Fabric Adapter (EFA)
    - Improved ENA for HPC,which is high performing computing only works for Linux
    - Great for inter-node communications, tightly coupled workloads
    - Leverages Message Passing Interface (MPI) standard
    - Bypasses the underlying Linux OS to provide low-latency, reliable transport

Low latency = ENA, HPC Cluster = EFA to improve performance

`modinfo ena` to make sure ENA is install on my ec2
`ethtool -i enX0` to get info about the driver (t2.micro)
`ethtool -i ens5` to get info about the driver on new generation of ec2 (t3.micro)


Placement group is when we wanna control how our ec2 instance is going to be placed on our infrastructure 
- Sometimes you want control over the EC2 Instance placement strategy
- That strategy can be defined using placement groups

When you create a placement group, you specify one of the 3 strategies for the group:
- Cluster—clusters instances into a low-latency group in a single Availability Zone. this will give you high performance, but high risk
- Spread—spreads instances across underlying hardware (max 7 instances per group per AZ) – critical applications
- Partition—spreads instances across many different partitions (which rely on different sets of racks) within an AZ. Scales to 100s of EC2 instances per group, allow to run app such as Hadoop, Cassandra, Kafka.

#### Cluster (all ec2 in same rack and same AZ)
Pros: Great network, low latency (10 Gbps bandwidth between instances with Enhanced Networking enabled - recommended)
Cons: If the rack fails, all instances fails at the same time
Use case:
- Big Data job that needs to complete fast
- Application that needs extremely low latency and high network throughput

#### Spread (ec2 in # rack and multiple AZ - max 7 instances/az)
Pros:
- Can span across Availability Zones (AZ)
- Reduced risk is simultaneous failure
- EC2 Instances are on different physical hardware
Cons:
- Limited to 7 instances per AZ per placement group
Use case:
- Application that needs to maximize high availability, and reduce risk
- Critical Applications where each instance must be isolated from failure from each other

#### Partition (many racks or partitions per az)
- Up to 7 partitions per AZ
- Can span across multiple AZs in the same region
- Up to 100s of EC2 instances
- The instances in a partition do not share racks with the instances in the other partitions
- A partition failure can affect many EC2 but won’t affect other partitions
- EC2 instances get access to the partition information as metadata
Use cases: 
- HDFS, HBase, Cassandra, Kafka

Enable termination protection:
To protect against accidental termination in AWS Console or CLI.
To terminate the instance we have to login into the instance and run the command `sudo shutdown`

### EC2 Launch Troubleshooting
#InstanceLimitExceeded: if you get this error, it means that you have reached your limit of max number of vCPUs per region
- On-Demand instance limits are set on a per-region basis
- Example: If you run on-demand (A, C, D, H, I, M, R, T, Z) instance types you’ll have 64 vCPUs (default)
Resolution: Either launch the instance in a different region or request AWS to increase your limit of the region
- NOTE: vCPU-based limits only apply to running On-Demand instances and
Spot instances

#InsufficientInstanceCapacity : if you get this error, it means AWS does not have that enough On-Demand capacity in the particular AZ where the instance is launched.
Resolution :
- Wait for few mins before requesting again.
- If requesting more than 1 requests, break down the requests. If you need 5 instances, rather than a single request of 5, request one by one.
- If urgent, submit a request for a different instance type now, which can be resized later.
- Also, can request the EC2 instance in a different AZ

#Instance Terminates Immediately (goes from pending to terminated)
- You've reached your EBS volume limit.
- An EBS snapshot is corrupt.
- The root EBS volume is encrypted and you do not have permissions to access the KMS key for decryption.
- The instance store-backed AMI that you used to launch the instance is missing a required part (an image.part.xx file).
To find the exact reason, check out the EC2 console of AWS - instances - Description tab, note the reason next to the State transition reason label.

### EC2 SSH troubleshooting
- Make sure the private key (pem file) on your linux machine has 400 permissions, else you will get “Unprotected private key file” error
- Make sure the username for the OS is given correctly when logging via SSH, else you will get “Host key not found”“Permission denied”, or “Connection closed by [instance] port 22” error
Possible reasons for “Connection timed out” to EC2 instance via SSH:
- SG is not configured correctly
- NACL is not configured correctly
- Check the route table for the subnet (routes traffic destined outside VPC to IGW)
- Instance doesn’t have a public IPv4
- CPU load of the instance is high

### EC2 Instances Purchasing Options
- On-Demand Instances – short workload, predictable pricing, pay by second
- Reserved (1 & 3 years)
   - Reserved Instances – long workloads (to run a DB for long time)
   - Convertible Reserved Instances – long workloads with flexible instances (if we change instance all the time)
- Savings Plans (1 & 3 years) –commitment to an amount of usage, long workload
- Spot Instances – short workloads, cheap, can lose instances (less reliable)
- Dedicated Hosts – book an entire physical server, control instance placement
- Dedicated Instances – no other customers will share your hardware
- Capacity Reservations – reserve capacity in a specific AZ for any duration

#### EC2 On Demand
- Pay for what you use:
    - Linux or Windows - billing per second, after the first minute
    - All other operating systems - billing per hour
- Has the highest cost but no upfront payment
- No long-term commitment
- Recommended for s`hort-term` and `un-interrupted` workloads, where you can't predict how the application will behave

#### EC2 Reserved Instances
- Up to `72%` discount compared to On-demand
- You reserve a specific instance attributes (Instance Type, Region, Tenancy, OS)
- `Reservation Period – 1 year` (+discount) or `3 years` (+++discount)
- `Payment Options – No Upfront` (+), `Partial Upfront` (++), `All Upfront` (+++)
- `Reserved Instance’s Scope – Regional` or `Zonal` (reserve capacity in an AZ)
- Recommended for steady-state usage applications (think database)
- You can buy and sell in the Reserved Instance Marketplace
- `Convertible Reserved Instance`
    - Can change the EC2 instance type, instance family, OS, scope and tenancy
    - Up to `66%` discount

#### EC2 Savings Plans
- Get a discount based on long-term usage (up to 72% - same as RIs)
- Commit to a certain type of usage ($10/hour for 1 or 3 years)
- Usage beyond EC2 Savings Plans is billed at the On-Demand price
- Locked to a specific instance family & AWS region (e.g., M5 in us-east-1)
- Flexible across:
    - Instance Size (e.g., m5.xlarge, m5.2xlarge)
    - OS (e.g., Linux, Windows)
    - Tenancy (Host, Dedicated, Default)

#### EC2 Spot Instances
- Can get a `discount of up to 90%` compared to On-demand
- Instances that you can “lose” at any point of time if your max price is less than the current spot price
- The `MOST cost-efficient` instances in AWS
- `Useful for workloads that are resilient to failure`
    - Batch jobs
    - Data analysis
    - Image processing
    - Any distributed workloads
    - Workloads with a flexible start and end time
- `Not suitable for critical jobs or databases`

EC2 Spot Instance Requests
- Define max spot price and get the instance while current spot price < max
    - The hourly spot price varies based on offer and capacity
    - If the current spot price > your max price you can choose to stop or terminate your instance with a 2 minutes grace period.
- Other strategy: Spot Block 
    - “block” spot instance during a specified time frame (1 to 6 hours) without interruptions
    - In rare situations, the instance may be reclaimed
- Used for batch jobs, data analysis, or workloads that are resilient to failures.
- Not great for critical jobs or databases

Spot Fleets (ultimate way to save money)
- Spot Fleets = set of Spot Instances + (optional) On-Demand Instances
- The Spot Fleet will try to meet the target capacity with price constraints you defined
    - Define possible launch pools: instance type (m5.large), OS, Availability Zone
    - Can have multiple launch pools, so that the fleet can choose the best lauch pool for you
    - Spot Fleet stops launching instances when reaching capacity or max cost
- Strategies to allocate Spot Instances:
    - `lowestPrice`: launch instances from the pool that has the lowest the lowest price (cost optimization, short workload)
    - diversified: launch instances will be distributed across all pools (great for availability, long workloads)
    - capacityOptimized: pool with the optimal capacity for the number of instances
    - priceCapacityOptimized (recommended): choosing pools with highest capacity available, then select the pool with the lowest price (best choice for most workloads)
- Spot Fleets allow us to automatically request Spot Instances with the lowest price

#### EC2 Dedicated Hosts
- A physical server with EC2 instance capacity fully dedicated to your use case
- Allows you address compliance requirements and use your existing serverbound software licenses (per-socket, per-core, pe—VM software licenses)
- Purchasing Options:
    - `On-demand` – pay per second for active Dedicated Host
    - `Reserved` - 1 or 3 years (No Upfront, Partial Upfront, All Upfront)
- The most expensive option
- Useful for software that have complicated licensing model (BYOL – Bring Your Own License)
- Or for companies that have strong regulatory or compliance needs

#### EC2 Dedicated Instances
- Instances run on hardware that’s dedicated to you
- May share hardware with other instances in same account
- No control over instance placement (can move hardware after Stop / Start)

#### EC2 Capacity Reservations
- Reserve `On-Demand` instances capacity in a specific AZ for any duration
- You always have access to EC2 capacity when you need it
- `No time commitment` (create/cancel anytime), `no billing discounts`
- Combine with Regional Reserved Instances and Savings Plans to benefit from billing discounts
- You’re charged at On-Demand rate whether you run instances or not
- Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ

Elastic IPs is a public IPv4 you own as long as you do not delete it.
You do not pay if Elastic IP is attached to a server, if it is not attached, you will pay for it (max of 5 per account)

### CloudWatch Metrics for EC2 (Important) ***
AWS Provided metrics (AWS pushes them):
    - Basic Monitoring (default): metrics are collected at a 5 minute internal
    - Detailed Monitoring (paid): metrics are collected at a 1 minute interval
    - Includes CPU, Network, Disk and Status Check Metrics
Custom metric (yours to push):
    - Basic Resolution: 1 minute resolution
    - High Resolution: all the way to 1 second resolution
    - Include RAM, application level metrics
    - Make sure the IAM permissions on the EC2 instance role are correct !

### EC2 included metrics (Important) ***
- CPU: CPU Utilization + Credit Usage / Balance
- Network: Network In / Out
- Status Check:
    - Instance status = check the EC2 VM is working
    - System status = check the underlying hardware is working
- Disk: Read / Write for Ops / Bytes (only for instance store)
- RAM is NOT included in the AWS EC2 metrics

### Unified CloudWatch Agent
- For virtual servers (EC2 instances, on-premises servers,…)
- Collect additional system-level metrics such as RAM,
processes, used disk space, etc.
- Collect logs to send to CloudWatch Logs
    - No logs from inside your EC2 instance will be sent to CloudWatch Logs without using an agent
- Centralized configuration using SSM Parameter Store
- Make sure IAM permissions are correct
- Default namespace for metrics collected by the Unified CloudWatch agent is CWAgent (can be configured/changed)

### Unified CloudWatch Agent – procstat Plugin ***
- Collect metrics and monitor system utilization of individual processes
- Supports both Linux and Windows servers
- Example: amount of time the process uses CPU, amount of memory the
process uses, …
- Select which processes to monitor by
    - pid_file: name of process identification number (PID) files they create
    - exe: process name that match string you specify (RegEx)
    - pattern: command lines used to start the processes (RegEx)
- Metrics collected by procstat plugin begins with “procstat” prefix (e.g.,procstat_cpu_time, procstat_cpu_usage, …)

*** TODO: Demo ***

### Status Checks ***
Status Checks Automated checks to identify hardware and software issues

- System Status Checks checks monitor the AWS systems on which your instance runs
    - Monitors problems with AWS systems (software/hardware issues on the physical host, loss of system power, …)
    - Check Personal Health Dashboard for any scheduled critical maintenance by AWS to your instance’s host
    - Problem with the underlying host. Example: 
       - Loss of network connectivity
       - Loss of system power
       - Software issues on the physical host
       - Hardware issues on the physical host that impact network reachability
    - Either wait for AWS to fix the host, OR
    - Move the EC2 instance to a new host = STOP & START the instance (if EBS backed)
    - Resolution: stop and start the instance (instance migrated to a new host)

- Instance Status Checks checks monitor the software and network configuration of your individual instance
    - Monitors software/network configuration of your instance (invalid network configuration, exhausted memory, …)
    - Example of issues
        - Incorrect networking or startup configuration
        - Exhausted memory
        - Corrupted file system
        - Incompatible kernel
    - Requires your involvement to fix
    - Restart the EC2 instance, OR
Change the EC2 instance configuration
    - Resolution: reboot the instance or change instance configuration

