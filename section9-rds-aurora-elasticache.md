# Amazon RDS Overview

## Introduction

RDS stands for **Relational Database Service**. It is a managed database service designed for databases that use **SQL (Structured Query Language)** as their query language. SQL is a structured language used to query and manage data in relational databases.

RDS allows you to create databases in the cloud that are fully managed by AWS, offering many operational and performance benefits.

---

## Supported Database Engines

Amazon RDS supports several database engines, including:

- PostgreSQL  
- MySQL  
- MariaDB  
- Oracle  
- Microsoft SQL Server  
- IBM DB2  
- Aurora (a proprietary AWS database)

It is important to remember these engines, as they are commonly referenced.

---

## Why Use RDS?

You might wonder why you would use RDS instead of deploying your own database on an EC2 instance. While running a database on EC2 is possible, RDS provides a **managed service** with many built-in features.

### Key Benefits of RDS

- **Automated provisioning** of databases  
- **Operating system patching** handled by AWS  
- **Continuous backups** with the ability to restore to a specific point in time (Point-in-Time Restore)  
- **Monitoring dashboards** for performance tracking  
- **Read replicas** to improve read performance  
- **Multi-AZ deployment** for high availability and disaster recovery  
- **Maintenance windows** for controlled upgrades  
- **Scalability options**:
  - Vertical scaling (increasing instance size)  
  - Horizontal scaling (adding read replicas)  
- **Storage backed by EBS**

One limitation is that you **cannot SSH into RDS instances**, since AWS manages the underlying infrastructure.

---

## RDS Storage Auto Scaling

A key feature of RDS is **Storage Auto Scaling**.

When creating an RDS database, you must define the initial storage size (e.g., 20 GB). However, if your database grows and begins running out of space, RDS can automatically increase storage if this feature is enabled.

### How It Works

Storage will automatically scale when:

- Free storage drops below **10%** of allocated storage  
- The low storage condition lasts for **at least 5 minutes**  
- At least **6 hours have passed** since the last storage modification  

Additionally, you must define a **maximum storage threshold** to prevent unlimited growth.

<img width="400" height="667" alt="image" src="https://github.com/user-attachments/assets/ba1e3670-73f7-4eb4-b78e-25376586f911" />

### Benefits

- Eliminates the need for manual storage scaling  
- Avoids downtime for storage upgrades  
- Ideal for applications with **unpredictable workloads**  
- Supported across all RDS database engines  

---

## Conclusion

Amazon RDS simplifies database management by automating operational tasks and providing scalability, availability, and performance enhancements. It is a powerful alternative to self-managed databases on EC2, especially for production workloads.

# RDS Read Replicas vs Multi-AZ

## Introduction

For the exam, it is extremely important to understand the difference between **RDS Read Replicas** and **Multi-AZ deployments**, as well as their specific use cases.

This guide focuses on clearly explaining both concepts.

---

## RDS Read Replicas

Read Replicas help you **scale read operations**.

### How It Works

- Your application sends **reads and writes** to the main RDS database (primary instance).
- If read traffic becomes too high, you can create up to **15 Read Replicas**.
- Replicas can be deployed:
  - Within the same Availability Zone (AZ)
  - Across different AZs
  - Across different regions

Replication between the primary database and replicas is **asynchronous**.

This means:
- Data is **eventually consistent**
- A replica may temporarily return **stale data**

### Architecture

![Read Replicas Architecture](https://github.com/user-attachments/assets/7fe7bce1-a461-4db9-90d0-52bc0ccbdf1b)

---

### Key Characteristics

- Used for **scaling reads**
- Supports **up to 15 replicas**
- Replication is **asynchronous**
- Replicas can be **promoted** to standalone databases
- Requires application to **update connection strings** to use replicas

---

### Important Limitation

Read Replicas are **read-only**:

- ✅ Allowed: `SELECT`
- ❌ Not allowed: `INSERT`, `UPDATE`, `DELETE`

---

## Read Replica Use Case

A common scenario:

- A production database handles normal application traffic.
- A new team wants to run **analytics/reporting**.
- Running analytics on the main DB would **impact performance**.

### Solution:
- Create a **Read Replica**
- Run analytics on the replica
- Keep production workload unaffected

### Use Case Diagram

![Read Replica Use Case](https://github.com/user-attachments/assets/a5956e87-cd46-482e-9410-de3f124a8c78)

---

## Read Replica Networking Costs

### Same Region

- Replication across AZs in the **same region is FREE**
- Example:
  - Primary: `us-east-1a`
  - Replica: `us-east-1b`
- No cross-AZ data transfer charges for replication

### Cross Region

- Replication across regions **incurs network costs**
- Example:
  - Primary: `us-east-1`
  - Replica: `eu-west-1`

### Diagram

![Network Cost](https://github.com/user-attachments/assets/a3024e01-7d5b-4a94-bfda-349b6231315e)

---

## RDS Multi-AZ

Multi-AZ is designed for **high availability and disaster recovery**, NOT scaling.

### How It Works

- A **primary (master)** database exists in one AZ
- A **standby** database exists in another AZ
- Replication is **synchronous**

This means:
- Every write must be confirmed on both primary and standby
- Data is always **consistent**

### Architecture

![Multi-AZ Architecture](https://github.com/user-attachments/assets/dddfa569-b187-4f95-a1fd-c2848f8079ac)

---

### Key Features

- **Synchronous replication**
- **Automatic failover**
- Single **DNS endpoint**
- No manual intervention required
- Used for **disaster recovery**

---

### Failover Behavior

If the primary fails due to:

- AZ failure
- Network issues
- Instance failure
- Storage failure

Then:
- Standby is automatically promoted to primary
- Application continues using the same DNS endpoint

---

### Important Limitation

- ❌ Cannot read from standby
- ❌ Cannot write to standby
- ❌ Does NOT improve performance

It is purely for **availability**

---

## Combining Read Replicas and Multi-AZ

Yes, you can:

- Configure **Read Replicas with Multi-AZ**
- This improves both:
  - **Read scalability**
  - **Disaster recovery**

This is a common **exam question**

---

## Converting Single-AZ to Multi-AZ

### Key Exam Point

- This is a **zero downtime operation**
- No need to stop the database

### How to Enable

- Go to database settings
- Click **Modify**
- Enable **Multi-AZ**

---

### What Happens Behind the Scenes

1. AWS takes a **snapshot** of the primary database  
2. The snapshot is **restored** into a standby instance  
3. **Synchronous replication** is established  
4. The standby catches up with the primary  

### Diagram

![Single AZ to Multi-AZ](https://github.com/user-attachments/assets/f8cd26df-f069-40ff-b009-5f1b5c0a96c2)

---

## Summary: Read Replicas vs Multi-AZ

| Feature              | Read Replicas              | Multi-AZ                     |
|---------------------|----------------------------|------------------------------|
| Purpose             | Scale reads                | High availability            |
| Replication         | Asynchronous               | Synchronous                  |
| Read capability     | Yes                        | No                           |
| Write capability    | No                         | No (standby only)            |
| Failover            | Manual (promote)           | Automatic                    |
| Performance boost   | Yes                        | No                           |

---

## Conclusion

Understanding the difference between **Read Replicas** and **Multi-AZ** is critical for the exam:

- Use **Read Replicas** for scaling reads  
- Use **Multi-AZ** for high availability and failover  

Both can be combined for a **robust and scalable architecture**.

# Amazon RDS Custom – Overview

## Introduction

RDS Custom is an extension of Amazon RDS that gives you **more control over the underlying operating system and database configuration**.

Unlike standard RDS, where AWS fully manages everything, RDS Custom allows deeper customization.

---

## Key Concept

With standard Amazon RDS:

- ❌ No access to the underlying OS  
- ❌ No SSH access  
- ❌ No deep system-level customization  

With RDS Custom:

- ✅ Access to the underlying operating system  
- ✅ Full database customization  
- ✅ Ability to SSH into the instance or use SSM Session Manager  

---

## Supported Engines

RDS Custom is available only for:

- Oracle  
- Microsoft SQL Server  

---

## What You Can Do with RDS Custom

With RDS Custom, you can:

- Configure internal database settings  
- Install OS-level patches  
- Enable native database features  
- Access the underlying EC2 instance  
- Connect using:
  - SSH  
  - AWS Systems Manager (SSM Session Manager)  

---

## How It Works

RDS Custom still provides managed service benefits such as:

- Automated setup  
- Managed scaling  
- AWS operational support  

However, unlike standard RDS, you also get access to the underlying infrastructure (EC2 instance).

---

## Example Concept

The database runs on an EC2-backed system.

With RDS Custom enabled, you can:

- SSH into the instance  
- Apply custom configurations  
- Modify OS-level settings  

---

## Automation Mode

When performing custom changes:

- It is recommended to **disable automation mode**
  - Prevents AWS from performing automatic maintenance or scaling during changes  

---

## Important Best Practice

Because you have low-level access:

- ⚠️ Always take a **database snapshot before making changes**
- This ensures you can recover if something breaks  

---

## RDS vs RDS Custom

| Feature | RDS | RDS Custom |
|--------|-----|------------|
| OS Access | ❌ No | ✅ Yes |
| Database Access | Limited | Full admin access |
| Management | Fully AWS-managed | Shared responsibility |
| Engines | Multiple | Oracle, SQL Server only |
| Customization | Limited | High |

---

## Summary

- **Amazon RDS** → fully managed, no OS access  
- **RDS Custom** → partial AWS management + full OS and DB control  
- Best for advanced use cases requiring deep customization of Oracle or SQL Server  

---

# Amazon Aurora – Overview

## Introduction

Amazon Aurora is a **proprietary AWS database technology** that often appears in the exam.

It is not open-source, but it is designed to be **compatible with MySQL and PostgreSQL**, meaning you can use existing drivers and tools as if you were connecting to a standard MySQL or PostgreSQL database.

---

## Compatibility

Aurora is compatible with:

- MySQL
- PostgreSQL

This means:
- Existing applications can connect without major changes
- Standard database drivers still work

---

## Performance Improvements

Aurora is cloud-optimized and provides:

- Up to **5x performance improvement over MySQL (RDS)**
- Up to **3x performance improvement over PostgreSQL (RDS)**

These improvements come from internal AWS optimizations (not something you manage directly).

---

## Storage Features

### Auto-Scaling Storage

- Starts at **10 GB**
- Automatically scales up to **256 TB**
- No manual intervention required

### Benefits

- No need to monitor disk usage manually
- Automatically grows with workload demand

---

## High Availability Design

Aurora is built for **high availability by default**.

### Key Concept

- Data is replicated **6 times across 3 Availability Zones (AZs)**

![Aurora High Availability](https://github.com/user-attachments/assets/87225789-2352-4d07-aa32-1760269c89a5)

### How It Works

- Only **4 out of 6 copies are needed for writes**
- Only **3 out of 6 copies are needed for reads**

This means:
- The system remains operational even if an AZ fails
- High durability and fault tolerance

---

## Self-Healing Storage

Aurora storage includes:

- Automatic **self-healing**
- Peer-to-peer replication
- Data repair in case of corruption
- Distributed storage across many volumes (internally managed)

---

## Architecture Overview

Aurora uses a **shared, distributed storage layer**:

- Storage is logical, not tied to a single instance
- Data is automatically replicated and striped across volumes
- Storage auto-expands as needed

![Aurora DB Cluster](https://github.com/user-attachments/assets/fcf00412-4c37-4665-9bd9-0b32303a0e50)

---

## Database Cluster Model

### Writer (Master) Instance

- Only one **writer instance**
- Handles all write operations
- Can fail over automatically

### Reader Replicas

- Up to **15 read replicas**
- Used to scale read workloads
- Can be distributed across regions
- Any replica can become the new writer during failover

---

## Failover Behavior

- Failover typically takes **less than 30 seconds**
- Faster than standard RDS Multi-AZ failover
- Highly optimized for availability

---

## Endpoints in Aurora

### Writer Endpoint

- DNS name that always points to the **current primary (writer)**
- Automatically updates during failover

### Reader Endpoint

- Load balances connections across read replicas
- Automatically distributes read traffic
- Works at **connection level (not query level)**

---

## Read Scaling

- Supports **auto-scaling of read replicas**
- Can scale between 1 and 15 replicas
- Improves performance for read-heavy workloads

---

## Cross-Region Replication

- Read replicas can be used across regions
- Useful for global applications and disaster recovery

---

## Key Features Summary

Aurora includes:

- Automatic failover
- Automated backups and recovery
- Continuous monitoring
- Automated patching (zero downtime)
- Auto-scaling compute and storage
- High security and compliance features

---

## Backtrack Feature

Aurora supports **Backtrack**, which allows:

- Rolling database back to a specific point in time
- No need to restore from backups
- Quick recovery from user mistakes

Example:

- Restore to 4:00 PM  
- Then adjust to 5:00 PM if needed  

---

## Summary

Key things to remember for the exam:

- Aurora = AWS proprietary high-performance database
- Compatible with MySQL and PostgreSQL
- Storage auto-scales (10 GB → 256 TB)
- 6 copies of data across 3 AZs
- Writer + up to 15 read replicas
- Writer and reader endpoints
- Fast failover (<30 seconds)
- Self-healing distributed storage
- Backtrack feature for time-based recovery

# Amazon Aurora – Advanced Concepts

## Introduction

These are advanced Aurora concepts that are important for the exam. Focus on understanding **how they work and when to use them**.

---

## Replica Auto Scaling

Aurora supports **automatic scaling of read replicas** based on load.

### How It Works

- You have:
  - 1 writer (via **writer endpoint**)  
  - Multiple readers (via **reader endpoint**)  

- When read traffic increases:
  - CPU usage on replicas rises  
  - Aurora automatically **adds new read replicas**  

- The **reader endpoint automatically includes new replicas**
- Traffic is distributed across all replicas

### Benefit

- Improved read performance  
- Reduced CPU load  
- No manual scaling required  

![Replica Auto Scaling](https://github.com/user-attachments/assets/bb1d6ede-5131-48f7-80da-5a4bc58b822b)

---

## Custom Endpoints

Custom endpoints allow you to **group specific replicas** for targeted workloads.

### Use Case

- Some replicas are more powerful (e.g., larger instance types)
- You want to dedicate them to **specific workloads** (e.g., analytics)

### How It Works

- Define a **custom endpoint** pointing to selected replicas
- Applications connect to this endpoint for specific queries

### Key Point

- After using custom endpoints, the default **reader endpoint is typically not used**
- You can create **multiple custom endpoints** for different workloads

![Custom Endpoints](https://github.com/user-attachments/assets/fb30e4bb-a502-4d8d-9dc4-7cdc62e27439)

---

## Aurora Serverless

Aurora Serverless provides **on-demand, auto-scaling database capacity**.

### Features

- No capacity planning required  
- Automatically scales based on usage  
- Pay per second of usage  

### Use Cases

- Infrequent workloads  
- Intermittent traffic  
- Unpredictable usage patterns  

### How It Works

- Client connects to a **proxy fleet**
- Aurora dynamically creates and scales database instances in the backend

![Aurora Serverless](https://github.com/user-attachments/assets/b09f44fc-9a0a-463c-a39a-3d1881202f38)

---

## Aurora Global Database

Aurora Global Database is designed for:

- **Disaster recovery**
- **Low-latency global reads**

### Architecture

- 1 **Primary region** (read + write)
- Up to **10 secondary regions** (read-only)
- Up to **16 read replicas per secondary region**

### Key Features

- Cross-region replication **< 1 second latency**
- Failover to another region in **< 1 minute (RTO)**

### Use Cases

- Global applications  
- Disaster recovery  

### Exam Tip

If you see:
> "Cross-region replication under 1 second"

→ Think **Aurora Global Database**

![Global Aurora](https://github.com/user-attachments/assets/fd5ad47e-1725-4108-91ff-35b4c77657a1)

---

## Aurora Machine Learning Integration

Aurora integrates directly with AWS ML services.

### Supported Services

- Amazon SageMaker  
- Amazon Comprehend  

### How It Works

- Application sends a **SQL query**
- Aurora sends data to ML service
- ML service returns predictions
- Results are returned via SQL

### Use Cases

- Fraud detection  
- Product recommendations  
- Sentiment analysis  
- Ad targeting  

![Aurora Machine Learning](https://github.com/user-attachments/assets/6729a2a4-4dfe-4604-9568-8bc7249db40d)

---

## Babelfish for Aurora PostgreSQL

Babelfish allows Aurora PostgreSQL to **understand Microsoft SQL Server queries (T-SQL)**.

### Problem

- SQL Server apps use:
  - T-SQL  
  - SQL Server drivers  

- PostgreSQL uses:
  - Different SQL dialect  

### Solution

Babelfish:

- Translates **T-SQL → PostgreSQL-compatible queries**
- Allows applications to:
  - Keep using the same driver  
  - Avoid major code changes  

### Benefits

- Easier migration from SQL Server  
- Minimal application changes  

### Migration Tools

- AWS SCT (Schema Conversion Tool)  
- AWS DMS (Database Migration Service)  

![Babelfish](https://github.com/user-attachments/assets/64049122-5abb-4a9e-a082-d0da513f083a)

---

## Summary

Key concepts to remember:

- **Auto Scaling** → Adds replicas automatically  
- **Custom Endpoints** → Target specific replicas  
- **Serverless** → No provisioning, pay-per-use  
- **Global Database** → Cross-region replication (<1s)  
- **Machine Learning** → SQL-based ML predictions  
- **Babelfish** → SQL Server compatibility for PostgreSQL


  # Amazon RDS & Aurora Backups, Restore, and Cloning

## Introduction

This section covers:

- RDS backups  
- Aurora backups  
- Restore options  
- Aurora database cloning  

These are important concepts for both **real-world usage and the exam**.

---

## RDS Backups

### Automated Backups

RDS automatically performs backups:

- **Daily full backup** during the backup window  
- **Transaction logs every 5 minutes**

### Key Benefits

- Enables **Point-in-Time Recovery (PITR)**
- You can restore to **any point up to 5 minutes ago**

### Retention

- Configurable from **1 to 35 days**
- Set to **0** to disable automated backups

---

### Manual DB Snapshots

- Triggered manually by the user  
- Stored until you delete them  

### Key Difference

| Feature | Automated Backup | Manual Snapshot |
|--------|----------------|-----------------|
| Trigger | Automatic | Manual |
| Retention | 1–35 days | Unlimited |
| Expiration | Yes | No |

---

### Cost Optimization Trick

If you rarely use a database:

1. Use the database  
2. Take a **manual snapshot**  
3. **Delete the database**  
4. Restore from snapshot when needed  

✅ Saves cost because:
- Snapshots are cheaper than running DB storage  

---

## Aurora Backups

Aurora backups are similar to RDS, with one key difference.

### Automated Backups

- Always enabled (❌ cannot disable)  
- Retention: **1 to 35 days**  
- Supports **Point-in-Time Recovery**

---

### Manual Snapshots

- User-triggered  
- Retained indefinitely  

---

## Restore Options

### 1. Restore from Backup or Snapshot

- Works for both **RDS and Aurora**
- Always creates a **new database instance**

---

### 2. Restore from Amazon S3 (RDS MySQL)

You can restore an RDS MySQL database from S3.

#### Process:

1. Backup your **on-premises database**
2. Upload backup file to **Amazon S3**
3. Restore into a **new RDS MySQL instance**

---

### 3. Restore to Aurora MySQL from S3

Aurora requires a specific backup format.

#### Process:

1. Backup database using **Percona XtraBackup**
2. Upload backup to **Amazon S3**
3. Restore into a **new Aurora MySQL cluster**

### Key Difference

| Target | Requirement |
|--------|------------|
| RDS MySQL | Standard backup |
| Aurora MySQL | Percona XtraBackup |

---

## Aurora Database Cloning

Aurora supports **fast database cloning**.

### Use Case

- Create a **staging/test environment** from production  
- Avoid impacting the production database  

---

### How It Works (Copy-on-Write)

1. Clone shares the **same underlying storage** initially  
2. No data is copied → **very fast**  
3. When changes occur:
   - Only modified data is copied  
   - Storage diverges over time  

---

### Benefits

- Extremely **fast creation**  
- **Cost-efficient** (no full copy initially)  
- Ideal for:
  - Testing  
  - Development  
  - Analytics  

---

## Summary

### RDS

- Automated backups (can disable)  
- Manual snapshots (unlimited retention)  

### Aurora

- Automated backups (cannot disable)  
- Manual snapshots  

### Restore

- Always creates a **new database**  
- Can restore from:
  - Snapshots  
  - S3 (with conditions)  

### Cloning (Aurora only)

- Fast, copy-on-write cloning  
- No initial data duplication  
- Ideal for staging environments

# Amazon RDS & Aurora Security

## Encryption at Rest

- Data is encrypted **on storage volumes**
- Uses **AWS KMS (Key Management Service)**
- Applies to:
  - Primary database
  - Read replicas
- Must be enabled **at database creation (launch time)**

### Important Constraints

- If the main database is **not encrypted**, replicas cannot be encrypted  
- Cannot directly encrypt an existing unencrypted database  

### How to Encrypt an Existing Database

1. Take a **snapshot** of the database  
2. Restore the snapshot with **encryption enabled**  

---

## Encryption in Transit (In-Flight)

- Data is encrypted **between client and database**
- Uses **TLS (Transport Layer Security)**
- Enabled by default

### Requirement

- Clients must use **AWS-provided TLS root certificates**

---

## Authentication

### Username & Password

- Standard database authentication method  

### IAM Authentication

- Use **IAM roles/users** instead of database credentials  
- Example:
  - EC2 instances can connect using their IAM role  

---

## Network Security

- Controlled using **Security Groups**

### You can define:

- Allowed IP addresses  
- Allowed ports (e.g., 3306)  
- Allowed security groups  

---

## SSH Access

- ❌ No SSH access (managed service)  
- ✅ Exception: **RDS Custom**

---

## Audit Logs

- Track database activity and queries  
- Stored temporarily by default  

### Long-Term Retention

- Send logs to **CloudWatch Logs**

# Amazon RDS Proxy

## Overview

Amazon RDS Proxy is a **fully managed database proxy** for RDS and Aurora.

It sits between your application and your database inside a VPC.

---

## Why RDS Proxy Exists

Normally:

- Applications connect **directly to RDS database instances**

With RDS Proxy:

- Applications connect to the **proxy instead**
- The proxy manages and optimizes database connections

---

## Connection Pooling

### Problem Without Proxy

- Each application creates its own connection to the database
- Many open connections can overload:
  - CPU
  - RAM
  - Database resources
- Can lead to **timeouts and performance issues**

---

### Solution With RDS Proxy

- RDS Proxy **pools and shares connections**
- Multiple application connections are grouped into fewer DB connections

### Result

- Fewer open connections to the database  
- Reduced load on DB resources  
- Better performance and stability  

---

## Performance Benefits (Exam Important ⚠️)

Using RDS Proxy:

- Reduces database stress (CPU + memory)
- Minimizes open connections
- Reduces timeouts
- Improves scalability under heavy load

---

## High Availability & Failover

### Key Feature

- RDS Proxy is **highly available across multiple AZs**

### Failover Improvement (Exam Important ⚠️)

- Reduces failover time by up to **66%**

---

### How It Works

Without proxy:
- Applications handle failover directly

With proxy:
- Applications connect only to RDS Proxy
- Proxy handles failover internally
- Applications remain unaware of failover events

---

## Supported Databases

RDS Proxy supports:

- MySQL  
- PostgreSQL  
- MariaDB  
- Microsoft SQL Server  
- Aurora MySQL  
- Aurora PostgreSQL  

---

## No Application Code Changes

- No need to modify application logic  
- Only change:
  - DB endpoint → RDS Proxy endpoint  

---

## IAM Authentication (Exam Important ⚠️)

RDS Proxy can enforce:

- **IAM-based authentication for databases**

### Benefits

- No need for database passwords in application code  
- Centralized security via IAM  

---

## AWS Secrets Manager Integration

- Database credentials can be securely stored in:
  - **AWS Secrets Manager**

---

## Security Characteristics

- RDS Proxy is **never publicly accessible**
- Only accessible **inside the VPC**

---

## RDS Proxy + AWS Lambda (Important Use Case)

### Problem Without Proxy

- AWS Lambda functions:
  - Scale rapidly
  - Create many short-lived connections
- This can:
  - Overload database
  - Cause connection exhaustion

---

### Solution With RDS Proxy

- Lambda functions connect to **RDS Proxy**
- Proxy:
  - Pools Lambda connections
  - Reduces number of DB connections
  - Prevents overload

---

## Key Idea

- Lambda may create **hundreds or thousands of connections**
- RDS Proxy converts them into a **small number of DB connections**

---

## Final Summary

RDS Proxy is used to:

- Pool database connections  
- Improve performance under heavy load  
- Reduce failover time (up to 66%)  
- Enforce IAM authentication  
- Secure credentials with Secrets Manager  
- Support high-churn workloads (like Lambda)  
- Provide a secure, non-public DB access layer

# Amazon ElastiCache

## Overview

Amazon ElastiCache is AWS’s **managed caching service**, similar in concept to how:

- RDS → managed relational databases  
- ElastiCache → managed in-memory caching systems  

It supports two engines:

- **Redis**
- **Memcached**

---

## What is a Cache?

- In-memory data storage system  
- Extremely **fast (low latency)**  
- Used to reduce load on databases  

---

## Why Use ElastiCache?

### Main Goal

- Reduce load on **RDS / databases**
- Improve performance for **read-heavy workloads**

---

## Core Idea

- Frequently accessed data is stored in cache  
- Database is only used when necessary  

---

## ElastiCache Architecture (Basic Pattern)

### Flow Overview

- Application checks cache first  
- If data exists → return immediately  
- If not → query database and store result in cache  

---

### Architecture Diagram

![ElastiCache Architecture](https://github.com/user-attachments/assets/4b09a331-ad04-4329-bd86-05d4757c5c41)

---

## Cache Behavior

### Cache Hit

- Data exists in ElastiCache  
- Returned directly  
- ❌ No database call  

---

### Cache Miss

- Data not found in cache  
- Query goes to database  
- Result stored back in cache for future use  

---

## Cache Invalidation (Important Concept)

- Cached data can become outdated  
- Requires a strategy to ensure consistency  

---

## User Session Store (Stateless Applications)

### Use Case

- Store user session data in cache instead of application memory  

---

### How It Works

1. User logs in  
2. Session stored in ElastiCache  
3. User connects to another app instance  
4. Session retrieved from cache  
5. User stays logged in  

---

### Benefit

- Makes application **stateless**
- Improves scalability and fault tolerance  

---

### Architecture Diagram

![User Session Store](https://github.com/user-attachments/assets/074ba5ab-f5df-42d7-b4fe-2ac00b5a80c6)

---

## Redis vs Memcached

ElastiCache supports two caching engines with different characteristics.

---

### Redis

- Multi-AZ support  
- Automatic failover  
- Read replicas for scaling  
- Data persistence (AOF)  
- Backup and restore support  

#### Advanced Features

- Data structures:
  - Sets  
  - Sorted sets  

#### Use Cases

- Leaderboards  
- Complex caching logic  
- High availability systems  

---

### Memcached

- Multi-node architecture  
- Data partitioning (sharding)  

#### Characteristics

- Very simple design  
- Extremely fast  
- No replication (standard setup)  
- No high availability  

#### Failure Behavior

- If a node fails → data is lost  

---

### Redis vs Memcached Comparison

![Redis vs Memcached](https://github.com/user-attachments/assets/a2c9ddc1-8ece-478f-ac9f-ece058ed725f)

---

## Exam Notes (Important ⚠️)

- Redis → feature-rich, durable, HA  
- Memcached → simple, fast, no persistence  

### Typical Choice Rule

- Use **Redis** unless simplicity is explicitly required  

---

## Final Summary

ElastiCache is used to:

- Improve application performance  
- Reduce database load  
- Cache frequent queries  
- Store user sessions  
- Enable stateless applications  
- Provide ultra-fast in-memory access to data  
# Amazon ElastiCache Security & Caching Patterns

## Overview

This lecture covers:

- ElastiCache security mechanisms (Redis + Memcached)
- Authentication options
- Encryption in transit
- Caching strategies
- Redis use case: gaming leaderboard (EXAM IMPORTANT)

---

# ElastiCache Security

## IAM Authentication

### Redis Only

- IAM authentication is supported **only for Redis**
- Used for **AWS API-level security**, not direct data access control inside Redis

### Important Clarification

- IAM policies do NOT directly secure Redis data access
- They are used to control **who can manage ElastiCache resources via AWS API**

---

## Redis AUTH (Important)

- Native Redis authentication method
- Uses:
  - Password
  - AUTH token

### How It Works

- Set during Redis cluster creation
- Clients must provide credentials to connect

### Security Layers

Redis security includes:

- Redis AUTH (password/token)
- Security Groups (network-level control)
- Optional encryption in transit

---

## Encryption in Transit (SSL)

- Supports **in-flight encryption using SSL/TLS**
- Protects data between:
  - Application → Redis cluster

---

## Memcached Security

- Uses **SASL-based authentication**
- Advanced authentication mechanism (name-level knowledge for exam)

---

# Cache Security Overview

## Security Layers Diagram

![Cache Security](https://github.com/user-attachments/assets/d72f5e7d-980f-4799-bc94-e2f8e46916bb)

---

# Security Architecture Example

## Typical Setup

- EC2 instance (client)
- Redis cluster

### Security Layers

- Redis AUTH (password/token)
- Security Group (network access control)
- Optional IAM authentication (Redis only)
- In-transit encryption (SSL)

---

# Caching Strategies (IMPORTANT)

## 1. Lazy Loading (Cache-Aside Pattern)

### How It Works

1. Application checks cache  
2. If **cache hit** → return data  
3. If **cache miss** → query database  
4. Store result in cache  

### Key Idea

- Data is loaded into cache **only when needed**

### Risk

- Cache can become **stale**
- Requires cache invalidation strategy

---

## 2. Write Through

### How It Works

- Every write goes to:
  - Database  
  - Cache  

### Benefits

- Cache always up to date  

### Trade-off

- Higher write latency  

---

## 3. Session Store Pattern

### Use Case

- Store user sessions in ElastiCache  

### How It Works

- Session stored in cache  
- Shared across multiple application instances  
- Uses TTL (Time To Live) for expiration  

---

# Lazy Loading Architecture

## Flow

1. Check cache  
2. If hit → return data  
3. If miss → query database  
4. Store result in cache  

---

# Redis Use Case (EXAM IMPORTANT ⚠️)

## Gaming Leaderboard

### Requirement

- Real-time ranking system:
  - Player #1  
  - Player #2  
  - Player #3  

---

## Redis Solution: Sorted Sets

### Features

- Unique elements  
- Automatically ordered by score  

### Behavior

- Every score update:
  - Rankings are updated instantly  
  - Data is sorted in real time  

---

## Result

- Real-time leaderboard  
- Shared across all clients  
- No application-side ranking logic needed  

---

## Exam Tip ⚠️

If question mentions:

- Leaderboard  
- Ranking system  
- Ordered scoring  

👉 Answer = **Redis Sorted Sets**

---

# Final Summary

## Security

- Redis AUTH = password/token  
- IAM auth = Redis API-level control  
- Memcached = SASL authentication  
- TLS = encryption in transit  

---

## Caching Strategies

- Lazy loading → cache on miss  
- Write-through → DB + cache writes  
- Session store → TTL-based sessions  

---

## Key Use Case

- Redis Sorted Sets → real-time leaderboards  

---

## Exam Highlights 🚨

- Cache invalidation is hard  
- Redis = advanced caching + real-time features  
- Memcached = simpler caching system

# AWS RDS & Aurora Quiz Notes

---

## Question 1
Amazon RDS supports the following databases, EXCEPT:
- MongoDB ✅
- MySQL
- MariaDB
- Microsoft SQL Server

---

## Question 2
You're planning for a new solution that requires a MySQL database that must be available even in case of a disaster in one of the Availability Zones. What should you use?
- Create Read Replicas
- Enable Encryption
- Enable Multi-AZ ✅

---

## Question 3
We have an RDS database that struggles to keep up with the demand of requests from our website. Our million users mostly read news, and we don't post news very often. Which solution is NOT adapted to this problem?
- An ElastiCache Cluster
- RDS Multi AZ ✅
- RDS Read Replicas

---

## Question 4
You have set up read replicas on your RDS database, but users are complaining that upon updating their social media posts, they do not see their updated posts right away. What is a possible cause for this?
- There's a bug in the app
- Read Replicas have ASYNC Replication, therefore it's likely your users will only read Eventual Consistency ✅
- You should have setup Multi-AZ instead

---

## Question 5
Which RDS (NOT Aurora) feature when used does not require you to change the SQL connection string?
- Multi-AZ ✅
- Read Replicas

---

## Question 6
Your application running on a fleet of EC2 instances managed by an Auto Scaling Group behind an Application Load Balancer. Users have to constantly log back in and you don't want to enable Sticky Sessions on your ALB as you fear it will overload some EC2 instances. What should you do?
- Use custom Load Balancer on EC2 instances instead of ALB
- Store session data in RDS
- Store session data in ElastiCache ✅
- Store session data in shared EBS volume

---

## Question 7
An analytics application is currently performing its queries against your main production RDS database. These queries run at any time of the day and slow down the RDS database which impacts your users' experience. What should you do to improve the users' experience?
- Setup a Read Replica ✅
- Setup a multi-AZ
- Run the analytics queries at night

---

## Question 8
You would like to ensure you have a replica of your database available in another AWS Region if a disaster happens to your main AWS Region. Which database do you recommend to implement this easily?
- RDS Read Replicas
- RDS Multi-AZ
- Aurora Read Replicas
- Aurora Global Database ✅

---

## Question 9
How can you enhance the security of your ElastiCache Redis Cluster by allowing users to access your ElastiCache Redis Cluster using their IAM Identities (e.g., Users, Roles)?
- Using Redis Authentification
- Using IAM Authentification ✅
- Use Security Groups

---

## Question 10
Your company has a production Node.js application that is using RDS MySQL 5.6 as its database. A new application programmed in Java will perform some heavy analytics workload to create a dashboard on a regular hourly basis. What is the most cost-effective solution you can implement to minimize disruption for the main application?
- Enable Multi-AZ for the RDS and run the analytics workload on the standby database
- Create a Read Replica in a different AZ and run the analytics workload on the replica database ✅
- Create a Read Replica in a different AZ and run the analytics workload on the source database

---

## Question 11
You would like to create a disaster recovery strategy for your RDS PostgreSQL database so that in case of a regional outage the database can be quickly made available for both read and write workloads in another AWS Region. The DR database must be highly available. What do you recommend?
- Create a Read Replica in the same region and enable Multi-AZ on the main database
- Create a Read Replica in a different region and enable Multi-AZ on the Read Replica ✅
- Create a Read Replica in the same region and enable Multi-AZ on the Read Replica
- Enable Multi Region option on the main database

---

## Question 12
You have migrated the MySQL database from on-premises to RDS. You have a lot of applications and developers interacting with your database. Each developer has an IAM user in the company's AWS account. What is a suitable approach to give access to developers to the MySQL RDS DB instance instead of creating a DB user for each one?
- By default IAM users have access to your RDS database
- Use Amazon Cognito
- Enable IAM Database Authentification ✅

---

## Question 13
Which of the following statement is true regarding replication in both RDS Read Replicas and Multi-AZ?
- Read Replica uses ASYNC replication and multi AZ uses ASYNC replication
- Read Replica uses ASYNC replication and multi AZ uses SYNC replication ✅
- Read Replica uses SYNC replication and multi AZ uses SYNC replication
- Read Replica uses SYNC replication and multi AZ uses ASYNC replication

---

## Question 14
How do you encrypt an unencrypted RDS DB instance?
- Create a snapshot of the unencrypted DB instance, copy the snapshot and enable encryption, then restore the RDS DB instance from the encrypted snapshot ✅

---

## Question 15
For your RDS database, you can have up to ............ Read Replicas.
- 5
- 15 ✅
- 7

---

## Question 16
Which RDS database technology does NOT support IAM Database Authentication?
- Oracle ✅
- PostgreSQL
- MySQL

---

## Question 17
You have an un-encrypted RDS DB instance and you want to create Read Replicas. Can you configure the RDS Read Replicas to be encrypted?
- NO ✅

---

## Question 18
An application running in production is using an Aurora Cluster as its database. Your development team would like to run a version of the application in a scaled-down application with the ability to perform some heavy workload on a need-basis. Most of the time, the application will be unused. Your CIO has tasked you with helping the team to achieve this while minimizing costs. What do you suggest?
- Use an Aurora Global Database
- Use an RDS database
- Use Aurora Serverless ✅
- Run Aurora on EC2, and write a script to shut down the EC2 instance at night

---

## Question 19
How many Aurora Read Replicas can you have in a single Aurora DB Cluster?
- 5
- 10
- 15 ✅

---

## Question 20
Amazon Aurora supports both .......................... databases.
- MySQL and PostgreSQL ✅

---

## Question 21
You work as a Solutions Architect for a gaming company. One of the games mandates that players are ranked in real-time based on their score. Your boss asked you to design then implement an effective and highly available solution to create a gaming leaderboard. What should you use?
- Use RDS for MySQL
- Use an Amazon Aurora
- Use ElastiCache for Memcached
- Use ElastiCache for Redis - Sorted Sets ✅

---

## Question 22
You need full customization of an Oracle Database on AWS. You would like to benefit from using the AWS services. What do you recommend?
- RDS for Oracle
- RDS Custom for Oracle ✅
- Deploy Oracle on EC2

---

## Question 23
You need to store long-term backups for your Aurora database for disaster recovery and audit purposes. What do you recommend?
- Enable Automated Backups
- Perform On Demand Backups ✅
- Use Aurora Database Cloning

---

## Question 24
Your development team would like to perform a suite of read and write tests against your production Aurora database because they need access to production data as soon as possible. What do you advise?
- Create an Aurora Read Replica for them
- Do the test against the production database
- Make a DB Snapshot and restore it into a new database
- Use the Aurora Cloning feature ✅

---

## Question 25
You have 100 EC2 instances connected to your RDS database and you see that upon a maintenance of the database, all your applications take a lot of time to reconnect to RDS, due to poor application logic. How do you improve this?
- Fix all the applications
- Disable Multi AZ
- Enable Multi AZ
- Use an RDS Proxy ✅

