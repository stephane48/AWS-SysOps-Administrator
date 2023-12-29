### AWS Snow Family
- Highly-secure, portable devices to **collect and process data at the edge**, and **migrate data into and out of AWS**
- **Data migration:** Snowcone, Snowball Edge, Snowmobile
- **Edge computing:** Snowcone, Snowball Edge

### Data Migrations with AWS Snow Family
**Challenges:**
- Limited connectivity
- Limited bandwidth
- High network cost
- Shared bandwidth (can’t maximize the line)
- Connection stability

**AWS Snow Family: offline devices to perform data migrations**
If it takes more than a week to transfer over the network, use Snowball devices!

### Snowball Edge (for data transfers)
- Physical data transport solution: move TBs or PBs of data in or out of AWS
- Alternative to moving data over the network (and paying network fees)
- Pay per data transfer job
- Provide block storage and Amazon S3-compatible object storage
- **Snowball Edge Storage Optimized**
    - 80 TB of HDD capacity for block volume and S3 compatible object storage
- **Snowball Edge Compute Optimized**
    - 42 TB of HDD or 28TB NVMe capacity for block volume and S3 compatible object storage
- Use cases: large data cloud migrations, DC decommission a data center, disaster recovery

### AWS Snowcone & Snowcone SSD
- **Small, portable computing, anywhere, rugged & secure, withstands harsh environments**
- Light (4.5 pounds, 2.1 kg)
- Device used for edge computing, storage, and data transfer
- **Snowcone** – 8 TB of HDD Storage
- **Snowcone SSD** – 14 TB of SSD Storage
- Use Snowcone where Snowball does not fit (spaceconstrained environment)
- Must provide your own battery / cables
- Can be sent back to AWS offline, or connect it to internet and use **AWS DataSync** to send data

### AWS Snowmobile
- Transfer exabytes of data (1 EB = 1,000 PB = 1,000,000 TBs)
- Each Snowmobile has 100 PB of capacity (use multiple in parallel)
- High security: temperature controlled, GPS, 24/7 video surveillance
- **Better than Snowball if you transfer more than 10 PB**

### Snow Family – Usage Process
1. Request Snowball devices from the AWS console for delivery
2. Install the snowball client / AWS OpsHub on your servers
3. Connect the snowball to your servers and copy files using the client
4. Ship back the device when you’re done (goes to the right AWS facility)
5. Data will be loaded into an S3 bucket
6. Snowball is completely wiped

### What is Edge Computing?
- Process data while it’s being created on an edge location
    - A truck on the road, a ship on the sea, a mining station underground...
- These locations may have
    - Limited / no internet access
    - Limited / no easy access to computing power
- We setup a **Snowball Edge / Snowcone** device to do edge computing
- Use cases of Edge Computing:
    - Preprocess data
    - Machine learning at the edge
    - Transcoding media streams
- Eventually (if need be) we can ship back the device to AWS (for transferring data for example)

### Snow Family – Edge Computing
- **Snowcone & Snowcone SSD (smaller)**
    - 2 CPUs, 4 GB of memory, wired or wireless access
    - USB-C power using a cord or the optional battery
- **Snowball Edge – Compute Optimized**
    - 104 vCPUs, 416 GiB of RAM
    - Optional GPU (useful for video processing or machine learning)
    - 28TB NVMe or 42TB HDD usable storage
    - Storage Clustering available (up to 16 nodes)
- **Snowball Edge – Storage Optimized**
    - Up to 40 vCPUs, 80 GiB of RAM, 80 TB storage
- All: Can run EC2 Instances & AWS Lambda functions (using AWS IoT Greengrass)
- Long-term deployment options: 1 and 3 years discounted pricing

### AWS OpsHub
- Historically, to use Snow Family devices, you needed a CLI (Command Line Interface tool)
- Today, you can use **AWS OpsHub** (a software you install on your computer / laptop) to manage your Snow Family Device
- Unlocking and configuring single or clustered devices
- Transferring files
- Launching and managing instances running on Snow Family Devices
- Monitor device metrics (storage capacity, active instances on your device)
- Launch compatible AWS services on your devices (ex: Amazon EC2 instances, AWS DataSync, Network File System (NFS))

### Amazon FSx – Overview
- **Allows you to Launch 3rd party high-performance file systems on AWS**
- Fully managed service

### Amazon FSx for Windows (File Server)
- **FSx for Windows** is a fully managed **Windows** file system share drive
- Supports SMB protocol & Windows NTFS
- Microsoft Active Directory integration, ACLs, user quotas
- **Can be mounted on Linux EC2 instances**
- Supports **Microsoft's Distributed File System (DFS) Namespaces** (group files across multiple FS)

- Scale up to 10s of GB/s, millions of IOPS, 100s PB of data
- Storage Options:
    - **SSD** – latency sensitive workloads (databases, media processing, data analytics, …)
    - **HDD** – broad spectrum of workloads (home directory, CMS, …)
- Can be accessed from your on-premises infrastructure (VPN or Direct Connect)
- Can be configured to be Multi-AZ (high availability)
- Data is backed-up daily to S3

### Amazon FSx for Lustre
- Lustre is a type of parallel distributed file system, for large-scale computing
- The name Lustre is derived from “Linux” and “cluster

- It's used for Machine Learning, **High Performance Computing (HPC)**
- Video Processing, Financial Modeling, Electronic Design Automation
- Scales up to 100s GB/s, millions of IOPS, sub-ms latencies
- Storage Options:
    - **SSD** – low-latency, IOPS intensive workloads, small & random file operations
    - **HDD** – throughput-intensive workloads, large & sequential file operations
- **Seamless integration with S3**
    - Can “read S3” as a file system (through FSx)
    - Can write the output of the computations back to S3 (through FSx)
- **Can be used from on-premises servers (VPN or Direct Connect)**

### FSx Lustre - File System Deployment Options
- **Scratch File System**
    - Temporary storage
    - Data is not replicated (doesn’t persist if file server fails)
    - High burst (6x faster, 200MBps per TiB)
    - Usage: short-term processing, optimize costs
- **Persistent File System**
    - Long-term storage
    - Data is replicated within same AZ
    - Replace failed files within minutes
    - Usage: long-term processing, sensitive data

### Amazon FSx for NetApp ONTAP
- Managed NetApp ONTAP on AWS
- **File System compatible with NFS, SMB, iSCSI protocol**
- Move workloads running on ONTAP or NAS to AWS
- Works with:
    - Linux
    - Windows
    - MacOS
    - VMware Cloud on AWS
    - Amazon Workspaces & AppStream 2.0
    - Amazon EC2, ECS and EKS
- Storage shrinks or grows automatically
- Snapshots, replication, low-cost, compression and data de-duplication
- **Point-in-time instantaneous cloning (helpful for testing new workloads)**

### Amazon FSx for OpenZFS
- Managed OpenZFS file system on AWS
- File System compatible with NFS (v3, v4, v4.1, v4.2)
- Move workloads running on ZFS to AWS
- Works with:
    - Linux
    - Windows
    - MacOS
    - VMware Cloud on AWS
    - Amazon Workspaces & AppStream 2.0
    - Amazon EC2, ECS and EKS
- Up to 1,000,000 IOPS with < 0.5ms latency
- Snapshots, compression and low-cost
- **Point-in-time instantaneous cloning (helpful for testing new workloads)**

### FSx for SysOps
- **FSx for Windows – Single-AZ**
    - Automatically replicates data within an AZ
    - Two generations: Single-AZ 1 (SSD), Single-AZ 2 (SSD & HDD)
- **FSx for Windows – Multi-AZ**
    - Automatically replicates data across AZs (synchronous)
    - Standby file server in a different AZ (automatic failover)

### Hybrid Cloud for Storage
- AWS is pushing for ”hybrid cloud”
    - Part of your infrastructure is on the cloud
    - Part of your infrastructure is on-premises
- This can be due to
    - Long cloud migrations
    - Security requirements
    - Compliance requirements
    - IT strategy
- S3 is a proprietary storage technology (unlike EFS / NFS), so how do you expose the S3 data on-premises?
- We use AWS Storage Gateway!

### AWS Storage Gateway
- Bridge between on-premises data and cloud data
- **Use cases:**
    - disaster recovery
    - backup & restore
    - tiered storage
    - on-premises cache & low-latency files access
- Types of Storage Gateway:
    - **S3 File Gateway**
    - **FSx File Gateway**
    - **Volume Gateway**
    - **Tape Gateway**

### Amazon S3 File Gateway
- Configured S3 buckets are accessible using the NFS and SMB protocol
- **Most recently used data is cached in the file gateway**
- Supports S3 Standard, S3 Standard IA, S3 One Zone A, S3 Intelligent Tiering
- **Transition to S3 Glacier using a Lifecycle Policy**
- Bucket access using IAM roles for each File Gateway
- SMB Protocol has integration with Active Directory (AD) for user authentication

### Amazon FSx File Gateway
- Native access to Amazon FSx for Windows File Server
- **Local cache for frequently accessed data**
- Windows native compatibility (SMB, NTFS, Active Directory...)
- Useful for group file shares and home directories

### Volume Gateway
- Block storage using iSCSI protocol backed by S3
- Backed by EBS snapshots which can help restore on-premises volumes!
- **Cached volumes:** low latency access to most recent data
- **Stored volumes:** entire dataset is on premise, scheduled backups to S3

### Tape Gateway
- Some companies have backup processes using physical tapes (!)
- With Tape Gateway, companies use the same processes but, in the cloud
- Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
- Back up data using existing tape-based processes (and iSCSI interface)
- Works with leading backup software vendors

### Storage Gateway – Hardware appliance
- Using Storage Gateway means you need on-premises virtualization
- Otherwise, you can use a **Storage Gateway Hardware Appliance**
- You can buy it on amazon.com
- Works with File Gateway, Volume Gateway, Tape Gateway
- Has the required CPU, memory, network, SSD cache resources
- Helpful for daily NFS backups in small data centers

### Amazon FSx File Gateway
- Native access to Amazon FSx for Windows File Server
- **Local cache for frequently accessed data**
- Windows native compatibility (SMB, NFTS, Active Directory...)
- Useful for group file shares and home directories

### Storage Gateway – SysOps ***
- File Gateway is POSIX compliant (Linux file system)
    - POSIX metadata ownership, permissions, and timestamps stored in the object’s metadata in S3
- Reboot Storage Gateway VM: (e.g., maintenance)
    - **File Gateway:** simply restart the Storage Gateway VM
    - **Volume and Tape Gateway:**
        - Stop Storage Gateway Service (AWS Console, VM local Console, Storage Gateway API)
        - Reboot the Storage Gateway VM
        - Start Storage Gateway Service (AWS Console, VM local Console, Storage Gateway API)

### Storage Gateway – Activations
- Two ways to get Activation Key:
    - Using the Gateway VM CLI
    - Make a web request to the Gateway VM (Port 80)
- Troubleshooting Activation Failures:
    - Make sure the Gateway VM has port 80 opened
    - Check that the Gateway VM has the correct time and synchronizing its time automatically to a Network Time Protocol (NTP) server

### Storage Gateway – Volume Gateway Cache
- Cached mode: only the most recent data is stored
- Looking at cache efficiency
    - Look at the CacheHitPercent metric (you want it to be high)
    - Look at the CachePercentUsed (you don’t want it to be too high)
- Create a larger cache disk
    - Use the cached volume to clone a new volume of a larger size
    - Select the new disk as the cached volume