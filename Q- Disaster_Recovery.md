### AWS DataSync ***
- Move large amount of data to and from places
    - On-premises / other cloud to AWS (NFS, SMB, HDFS, S3 API…) – **needs agent**
    - AWS to AWS (different storage services) – no agent needed
- Can synchronize to:
    - Amazon S3 (any storage classes – including Glacier)
    - Amazon EFS
    - Amazon FSx (Windows, Lustre, NetApp, OpenZFS...)
- Replication tasks can be scheduled hourly, daily, weekly
- **File permissions and metadata are preserved** (NFS POSIX, SMB…)
- One agent task can use 10 Gbps, can setup a bandwidth limit

### AWS DataSync NFS / SMB to AWS (S3, EFS, FSx…)

### AWS DataSync Transfer between AWS storage services

### AWS Backup
- Fully managed service
- It allows you to Centrally manage and automate backups across AWS services
- No need to create custom scripts and manual processes
- Supported services:
    - Amazon EC2 / Amazon EBS
    - Amazon S3
    - Amazon RDS (all DBs engines) / Amazon Aurora / Amazon DynamoDB
    - Amazon DocumentDB / Amazon Neptune
    - Amazon EFS / Amazon FSx (Lustre & Windows File Server)
    - AWS Storage Gateway (Volume Gateway)
- Supports cross-region backups
- Supports cross-account backups

### AWS Backup
- Supports PITR (Point In Time Recovery) for supported services
- On-Demand and Scheduled backups
- Tag-based backup policies
- You create backup policies known as **Backup Plans**
    - Backup frequency (every 12 hours, daily, weekly, monthly, cron expression)
    - Backup window
    - Transition to Cold Storage (Never, Days, Weeks, Months, Years)
    - Retention Period (Always, Days, Weeks, Months, Years)

### AWS Backup

### AWS Backup Vault Lock
- Enforce a WORM (Write Once Read Many) state for all the backups that you store in your AWS Backup Vault
- Additional layer of defense to protect your backups against:
    - Inadvertent or malicious delete operations
    - Updates that shorten or alter retention periods
- Even the root user cannot delete backups when enabled

Backup Vault Lock Policy - Backups can’t be deleted