### Amazon RDS Overview
- RDS stands for Relational Database Service
- It’s a managed DB service for DB use SQL as a query language.
- It allows you to create databases in the cloud that are managed by AWS
    - Postgres
    - MySQL
    - MariaDB
    - Oracle
    - Microsoft SQL Server
    - Aurora (AWS Proprietary database)

### Advantage over using RDS vs deploying DB on EC2
- RDS is a managed service:
    - Automated provisioning, OS patching
    - Continuous backups and restore to specific timestamp (Point in Time Restore)!
    - Monitoring dashboards
    - Read replicas for improved read performance
    - Multi AZ setup for DR (Disaster Recovery)
    - Maintenance windows for upgrades
    - Scaling capability (vertical and horizontal)
    - Storage backed by EBS (gp2 or io1)
- BUT you can’t SSH into your instances

### RDS – Storage Auto Scaling ***
- Helps you increase storage on your RDS DB instance dynamically
- When RDS detects you are running out of free database storage, it scales automatically
- Avoid manually scaling your database storage
- You have to set **Maximum Storage Threshold** (maximum limit for DB storage)
- Automatically modify storage if:
    - Free storage is less than 10% of allocated storage
    - Low-storage lasts at least 5 minutes
    - 6 hours have passed since last modification
- Useful for applications with **unpredictable workloads**
- Supports all RDS database engines (MariaDB, MySQL, PostgreSQL, SQL Server, Oracle)

### RDS Read Replicas for read scalability ***
Read replicas help you to scale your reads
- Up to 15 Read Replicas
- Within AZ, Cross AZ or Cross Region
- Replication is **ASYNC**, so reads are eventually consistent
- Replicas can be promoted to their own DB
- Applications must update the connection string to leverage read replicas

### RDS Read Replicas – Use Cases
- You have a production database that is taking on normal load
- You want to run a reporting application to run some analytics
- You create a Read Replica to run the new workload there
- The production application is unaffected
- Read replicas are used for SELECT (=read) only kind of statements (not INSERT, UPDATE, DELETE)

### RDS Read Replicas – Network Cost
- In AWS there’s a network cost when data goes from one AZ to another
- **For RDS Read Replicas within the same region or different AZ, you don’t pay that fee**

### RDS Multi AZ (Disaster Recovery)
- **SYNC** replication
- One DNS name – automatic app failover to standby
- Increase **availability**
- Failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps
- Not used for scaling
- Note:The Read Replicas be setup as Multi AZ for Disaster Recovery (DR)

### RDS – From Single-AZ to Multi-AZ ***
- Zero downtime operation (no need to stop the DB)
- Just click on “modify” for the database
- The following happens internally:
    - A snapshot is taken
    - A new DB is restored from the snapshot in a new AZ
    - Synchronization is established between the two databases

### RDS Multi AZ – Failover Conditions
- The primary DB instance
    - Failed
    - OS is undergoing software patches
    - Unreachable due to loss of network connectivity
    - Modified (e.g., DB instance type changed)
    - Busy and unresponsive
    - Underlying storage failure
- An Availability Zone outage
- A manual failover of the DB instance was initiated using **Reboot with failover**

### Lambda by default
- By default, your Lambda function is launched outside your own VPC (in an AWS-owned VPC)
- Therefore, it cannot access resources in your VPC (RDS, ElastiCache, internal ELB…)

### Lambda in VPC
- You must define the VPC ID, the Subnets and the Security Groups
- Lambda will create an ENI (Elastic Network Interface) in your subnets
- **AWSLambdaVPCAccessExecutionRole**

### RDS Proxy for AWS Lambda
- When using Lambda functions with RDS, it opens and maintains a database connection
- This can result in a **“TooManyConnections”** exception
- With RDS Proxy, you no longer need code that handles cleaning up idle connections and managing connection pools
- Supports IAM authentication or DB authentication, auto-scaling
- The Lambda function must have connectivity to the Proxy (public proxy => public Lambda, private proxy => Lambda in VPC)

### DB Parameter Groups
- You can configure the DB engine using Parameter Groups
- Dynamic parameters are applied immediately
- Static parameters are applied after instance reboot
- You can modify parameter group associated with a DB (must reboot)
- See documentation for list of parameters for a DB technology

- **Must-know parameter:**
    - PostgreSQL / SQL Server: **rds.force_ssl=1** => force SSL connections
    - MySQL / MariaDB: **require_secure_transport=1** => force SSL connections

### RDS Backup vs. Snapshots
Backups
- Backups are “continuous” and allow point in time recovery
- Backups happen during maintenance windows
- When you delete a DB instance, you can retain automated backups
- Backups have a retention period you set between 0 and 35 days
- To disable backups, set retention period to 0
Snapshots
- Snapshots takes IO operations and can stop the database from seconds to minutes
- Snapshots taken on Multi AZ DB don’t impact the master – just the standby
- Snapshots are incremental after the first snapshot (which is full)
- You can copy & share DB Snapshots
- **Manual** Snapshots don’t expire
- You can take a “final snapshot” when you delete your DB

Restoring from Automated Backups or DB Snapshots creates a new DB Instance

### RDS Snapshots Sharing
- **Manual snapshots:** can be shared with AWS accounts
- **Automated snapshots:** can’t be shared, copy first
- You can only share unencrypted snapshots and snapshots encrypted with a customer managed key
- If you share an encrypted snapshots, you must also share any customer managed keys used to encrypt them

### RDS Events & Event Subscriptions
- RDS keeps record of events related to:
    - DB instances
    - Snapshots
    - Parameter groups, security groups …
- Example: DB state changed from pending to running
- **RDS Event Subscriptions**
    - Subscribe to events to be notified when an event occurs using SNS
    - Specify the Event Source (instances, SGs, …) and the Event Category (creation, failover, …)
- RDS delivers events to EventBridge

### RDS with CloudWatch
- CloudWatch metrics associated with RDS (gathered from the hypervisor):
    - DatabaseConnections
    - SwapUsage
    - ReadIOPS / WriteIOPS
    - ReadLatency / WriteLatency
    - ReadThroughPut / WriteThroughPut
    - DiskQueueDepth
    - FreeStorageSpace
- Enhanced Monitoring (gathered from an agent on the DB instance)
    - Useful when you need to see how different processes or threads use the CPU
    - Access to over 50 new CPU, memory, file system, and disk I/O metrics

### RDS Performance Insights
- Visualize your database performance and analyze any issues that affect it
- With the Performance Insights dashboard, you can visualize the database load and filter the load:
    - By Waits => find the resource that is the bottleneck (CPU, IO, lock, etc…)
    - By SQL statements => find the SQL statement that is the problem
    - By Hosts => find the server that is using the most our DB
    - By Users => find the user that is using the most our DB
- DBLoad = the number of active sessions for the DB engine
- You can view the SQL queries that are putting load on your database

### Amazon Aurora ***
- Aurora is a proprietary technology from AWS (not open sourced)
- Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or MySQL database)
- Aurora is “AWS cloud optimized” and claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS
- Aurora storage automatically grows in increments of 10GB, up to 128 TB.
- Aurora can have up to 15 replicas and the replication process is faster than MySQL (sub 10 ms replica lag)
- Failover in Aurora is instantaneous. It’s HA (High Availability) native.
- Aurora costs more than RDS (20% more) – but it is more efficient

### Aurora High Availability and Read Scaling
- 6 copies of your data across 3 AZ:
    - 4 copies out of 6 needed for writes
    - 3 copies out of 6 need for reads
    - Self healing with peer-to-peer replication
    - Storage is striped across 100s of volumes
- One Aurora Instance takes writes (master)
- Automated failover for master in less than 30 seconds if the master does not work
- Master + up to 15 Aurora Read Replicas serve reads
- **Support for Cross Region Replication**

### Aurora DB Cluster
- Writer Endpoint it is a DNS name that is always pointing to the master, even if the master failover, client can talk to the writer point is automatically redirected to the right instance.
- Reader Endpoint helps connection Load Balancing, and connects automatically to the all the read replicas, and client can connect to reader endpoint to read replicas
- Auto Scaling is attached to the replicas that will scale the replicas (up to 15 replicas)
- Shared storage Volume which is auto expanding from 10G to 128 TB
- Master is the only thing that will write to the storage

### Features of Aurora
- Automatic fail-over
- Backup and Recovery
- Isolation and security
- Industry compliance
- Push-button scaling
- Automated Patching with Zero Downtime
- Advanced Monitoring
- Routine Maintenance
- Backtrack: restore data at any point of time without using backups

### Backups, Backtracking & Restores in Aurora
- **Automatic Backups**
    - Retention period 1-35 days (can’t be disabled)
    - PITR (Point In Time Recovery), restore your DB cluster within 5 minutes of the current time
    - Restore to a new DB cluster
- **Aurora Backtracking**
    - Rewind the DB cluster back and forth in time (up to 72 hours)
    - Doesn’t create a new DB cluster (in-place restore)
    - Supports Aurora MySQL only
- **Aurora Database Cloning**
    - Creates a new DB cluster that uses the same DB cluster volume as the original cluster
    - Uses copy-on-write protocol (use the original/single copy of the data and allocate storage only when changes made to the data)
    - Example: create a test environment using your production data

### Aurora for SysOps
- You can associate a priority tier (0-15) on each Read Replica
    - Controls the failover priority
    - RDS will promote the Read Replica with the highest priority (lowest tier)
    - If replicas have the same priority, RDS promotes the largest in size
    - If replicas have the same priority and size, RDS promotes arbitrary replica (randomly)

- You can migrate an RDS MySQL snapshot to Aurora MySQL Cluster

### RDS & Aurora Security
- **At-rest encryption:**
    - Database master & replicas encryption using AWS KMS – must be defined as launch time
    - If the master is not encrypted, the read replicas cannot be encrypted
    - To encrypt an un-encrypted database, go through a DB snapshot & restore as encrypted
- **In-flight encryption:** TLS-ready by default, use the AWS TLS root certificates client-side
- **IAM Authentication:** IAM roles to connect to your database (instead of username/pw)
- **Security Groups:** Control Network access to your RDS / Aurora DB
- **No SSH available** except on RDS Custom
- ***Audit Logs can be enabled** *and sent to CloudWatch Logs for longer retention

### Aurora: CloudWatch metrics
- **AuroraReplicaLag**: amount of lag when replicating updates from the primary instance
- **AuroraReplicaLagMaximum**: max. amount of lag across all DB instances in the cluster
- **AuroraReplicaLagMinimum**: min. amount of lag across all DB instances in the cluster
- If replica lag is high, that means the users will have a different experience based on which replica they get the data from (eventual consistency)
- **DatabaseConnections**: current number of connections to a DB instance
- **InsertLatency**: average duration of insert operations

### Amazon ElastiCache Overview
- The same way RDS is to get managed Relational Databases…
- ElastiCache is to get managed Redis or Memcached
- Caches are in-memory databases with really high performance, low latency
- Helps reduce load off of databases for read intensive workloads
- Helps make your application stateless by putting the state of you app in Amazon ElasticCache
- AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups
- **Using ElastiCache involves heavy application code changes**

### ElastiCache Solution Architecture - DB Cache
- Applications queries ElastiCache, if not available, get from RDS and store in ElastiCache.
- Helps relieve load in RDS
- Cache must have an invalidation strategy to make sure only the most current data is used in there.

### ElastiCache Solution Architecture – User Session Store
- User logs into any of the application
- The application writes the session data into ElastiCache
- The user hits another instance of our application
- The instance retrieves the data and the user is already logged in

### ElastiCache – Redis vs Memcached
REDIS
- **Multi AZ** with Auto-Failover
- **Read Replicas** to scale reads and have **high availability**
- Data Durability using AOF persistence
- **Backup and restore features**
- Supports Sets and Sorted Sets
MEMCACHED
- Multi-node for partitioning of data (sharding)
- **No high availability (replication)**
- **Non persistent**
- **No backup and restore**
- Multi-threaded architecture

### ElastiCache Replication: Cluster Mode Disabled
- One primary node, up to 5 replicas
- Asynchronous Replication
- The primary node is used for read/write
- The other nodes are read-only
- **One shard, all nodes have all the data**
- Guard against data loss if node failure
- Multi-AZ enabled by default for failover
- Helpful to scale read performance

### Redis Scaling – Cluster Mode Disabled
- **Horizontal:**
    - Scale out/in by adding/removing read replicas (max. 5 replicas)
- **Vertical:**
    - Scale up/down to larger/smaller node type
    - ElastiCache will internally create a new node group, then data replication and DNS update

### ElastiCache Replication: Cluster Mode Enabled
- **Data is partitioned across shards (helpful to scale writes)**
- Each shard has a primary and up to 5 replica nodes (same concept as before)
- Multi-AZ capability
- Up to 500 nodes per cluster:
    - 500 shards with single master
    - 250 shards with 1 master and 1 replica
    - …
    - 83 shards with one master and 5 replicas

### ElastiCache for Redis – Auto Scaling
- Automatically increase/decrease the desired shards or replicas
- Supports both Target **Tracking and Scheduled Scaling Policies**
- Works only for Redis with **Cluster Mode Enabled**

### ElastiCache – Redis Connection Endpoints
- **Standalone Node**
- One endpoint for read and write operations
- **Cluster Mode Disabled Cluster**
    - **Primary Endpoint** – for all write operations
    - **Reader Endpoint** – evenly split read operations across all read replicas
    - **Node Endpoint** – for read operations
- **Cluster Mode Enabled Cluster**
    - **Configuration Endpoint** – for all read/write operations that support Cluster Mode Enabled commands
    - **Node Endpoint** – for read operations

### Redis Scaling – Cluster Mode Disabled ***
- **Horizontal:**
    - Scale out/in by adding/removing read replicas (max. 5 replicas)
- **Vertical:**
    - Scale up/down to larger/smaller node type
    - ElastiCache will internally create a new node group, then data replication and DNS update

### Redis Scaling – Cluster Mode Enabled
- Two Modes:
    - Online Scaling: continue serving requests during the scaling process (no downtime, some degradation in performance)
    - Offline Scaling: unable to serve requests during the scaling process (backup and restore). Additional configurations supported (change node type, upgrade engine version, …)
- **Horizontal:** (Resharding and Shard Rebalancing)
    - Resharding: scale out/in by adding/removing shards
    - Shard Rebalancing: equally distribute the keyspaces among the shards as possible
    - Supports Online and Offline Scaling
- **Vertical:** (change read/write capacity)
    - Scale up/down to larger/smaller node type
    - Supports Online Scaling

### Redis Metrics to Monitor
- **Evictions:** the number of non-expired items the cache evicted to allow space for new writes (memory is overfilled). Solution:
    - Choose an eviction policy to evict expired items (e.g., evict least recently used (LRU) items)
    - Scale up to larger node type (more memory) or scale out by adding more nodes
- **CPUUtilization:** monitor CPU utilization for the entire host
    - Solution: scale up to larger node type (more memory) or scale out by adding more nodes
- **SwapUsage:** should not exceed 50 MB
    - Solution: verify that you have configured enough reserved memory
- **CurrConnections:** the number of concurrent and active connections
    - Solution: investigate application behavior to address the issue
- **DatabaseMemoryUsagePercentage:** the percentage of memory utilization
- **NetworkBytesIn/Out & NetworkPacketsIn/Out**
- **ReplicationBytes:** the volume of data being replicated
- **ReplicationLag:** how far behind the replica is from the primary node

### Memcached Scaling
- Memcached Clusters can have 1-40 nodes (soft limit)
- **Horizontal:**
    - Add/remove nodes from the cluster
    - Auto-discovery allow your app to find nodes
- **Vertical:**
    - Scale up/down to larger/smaller node type
    - Scale up process:
        - Create a new cluster with the new node type
        - Update your application to use the new cluster’s endpoints
        - Delete the old cluster
    - Memcached clusters/nodes start out empty

### Memcached Auto Discovery

- Typically you need to manually connect to individual cache nodes in the cluster using its DNS endpoints
- Auto Discovery automatically identifies all of the nodes
- All the cache nodes in the cluster maintain a list of metadata about all other nodes
- This is seamless from a client perspective

### Memcached Metrics to Monitor
- **Evictions:** the number of non-expired items the cache evicted to allow space for new writes (memory is overfilled). Solution:
    - Choose an eviction policy to evict expired items (e.g., LRU items)
    - Scale up to larger node type (more memory) or scale out by adding more nodes
- **CPUUtilization**
    - Solution: scale up to larger node type or scale out by adding more nodes
- **SwapUsage:** should not exceed 50 MB
- **CurrConnections:** the number of concurrent and active connections
    - Solution: investigate application behavior to address the issue
- **FreeableMemory:** amount of free memory on the host