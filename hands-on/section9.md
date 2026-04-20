# Amazon RDS Hands-On

## Step 1: Create Database

1. Go to AWS Console  
2. Search for Aurora and RDS  
3. Navigate to:  
   Databases → Create database  
4. Choose Full configuration (Standard Create)  

---

## Step 2: Configure Database

### Engine

- Select MySQL  

---

### Credentials

- AWS Secrets Manager is available but not in Free Tier  
- Choose Self-managed credentials  
- Set:
  - Username: admin  
  - Password: (your choice)  

---

### Settings to Modify

Keep everything default except:

#### Public Access

- Enable Public access  
  - Allows connection from outside the VPC  

#### Security Group

- Create a new VPC Security Group:
  - Name: demo-rds  

#### Port

- Keep default:
  - 3306 (MySQL)  

---

## Step 3: Launch Database

- Click Create database  
- Wait until status = Available  

---

## Step 4: Connect with DBeaver

Use the following:

- Endpoint (from AWS)
- Username: admin  
- Password: your password  

---

### Finding the Endpoint

Go to:
RDS → Database → Connectivity & Security  

![RDS Endpoint](https://github.com/user-attachments/assets/a5313da0-c960-49f3-b7d4-e3ae47b98886)

---

## Step 5: Run SQL Commands

### Create Table

CREATE TABLE mytable;

### Insert Data

INSERT INTO mytable (name, first_name)
VALUES ("Greksza", "Dalma");

---

## Step 6: Monitoring

- Go to Monitoring tab to view:
  - CPU usage  
  - Connections  
  - Storage metrics  

---

## Step 7: Actions Menu

From Actions, you can:

- Take snapshots  
- Restore to a point in time  
- Migrate snapshots  

---

## Step 8: Cleanup

After finishing:

- Go to Actions → Delete database  

⚠️ Important: Always delete resources to avoid unexpected charges

# Amazon Aurora Hands-On

> ⚠️ **Important**: Only perform this hands-on if you are willing to incur AWS costs.

---

## Step 1: Create Database

1. Go to AWS Console  
2. Navigate to **RDS → Databases → Create database**  
3. Choose **Standard Create**

---

## Step 2: Engine Selection

- Select **Amazon Aurora**
- Choose **Aurora (MySQL-compatible)**
- Keep the **default version**

---

## Step 3: Templates & Credentials

### Template

- Select **Production**

### Credentials

- Set:
  - **DB cluster identifier** (name)
  - **Admin username**
  - **Password**

---

## Step 4: Cluster Storage Configuration

Choose one:

- **Aurora Standard** → Cost-effective workloads  
- **Aurora I/O-Optimized** → Higher performance, higher cost  

---

## Step 5: Instance Configuration

- Choose instance type based on performance needs  
- Typically use **burstable classes** for testing  

### Optional: Serverless v2

- Configure **Aurora Capacity Units (ACUs)**
- Enables automatic scaling of compute capacity  

---

## Step 6: Availability & Durability

- Enable:
  - **Create an Aurora Replica (Reader) in a different AZ**

> ⚠️ This increases cost but improves availability

---

## Step 7: Connectivity

- Do **NOT** connect to an EC2 compute resource  
- Network type: **IPv4**  
- VPC: **Default VPC**  
- Subnet group: **Default DB subnet group**  

### Public Access

- Enable **Public access**

### Security Group

- Create a new VPC Security Group:
  - Name: `demo-database-aurora`

### Port

- Keep default:
  - **3306**

---

## Step 8: Additional Configuration

- Set **Initial database name** (optional)  
- Backup retention:
  - **1 day**  
- Enable/disable:
  - Encryption  
  - Deletion protection  

---

## Step 9: Launch Cluster

- Click **Create database**
- Wait until the cluster is available

---

## Step 10: Cluster Architecture

Once created:

- 1 **Writer instance** (handles writes)  
- 1 **Reader instance** (handles reads)  
- Instances are in **different AZs (same region)**  

---

## Step 11: Scaling & Replicas

### Add Reader Nodes

- You can manually add more **read replicas**

### Auto Scaling

- Configure **Auto Scaling policy** for replicas:
  - Set **minimum** number of replicas  
  - Set **maximum** number of replicas  


![Aurora Cluster](https://github.com/user-attachments/assets/528077ce-ebfe-4c4b-a17d-5d7a313b44f8)

---

## Summary

- Aurora cluster = **1 writer + multiple readers**
- Readers can be placed across AZs
- Supports **auto scaling of replicas**
- Serverless option available (ACUs)
- Higher availability comes with higher cost


# Amazon ElastiCache Hands-On

## Overview

This hands-on covers how to create and configure an **Amazon ElastiCache (Redis) cluster** using the AWS console.

---

## Step 1: Choose Engine

When starting the setup, you have multiple engine options:

- **Valkey** (Redis-compatible, recommended replacement for Redis)
- **Memcached**
- **Redis OSS**

👉 In this hands-on, we choose:
- **Redis**

---

## Step 2: Deployment Option

You can choose between:

- **Serverless**
- **Node-based cluster**

👉 For learning purposes, we choose:
- **Node-based cluster** (to understand architecture clearly)

---

## Step 3: Creation Mode

You have two options:

- **Easy Create**
  - Uses best-practice defaults
  - Production / dev-test / demo presets

- **Custom Configuration**
  - Full control over settings

👉 We choose:
- **Custom configuration** (to see all options)

---

## Step 4: Cluster Mode

- **Cluster mode disabled**:
  - Single shard
  - 1 primary node
  - Up to 5 read replicas

- **Cluster mode enabled**:
  - Multiple shards
  - Distributed scaling across nodes

👉 We choose:
- **Cluster mode: Disabled**

---

## Step 5: Cluster Details

- Cluster name: `DemoCluster`
- Deployment location:
  - AWS Cloud (default)
  - Optional: AWS Outposts (on-premises support)

---

## Step 6: High Availability (Multi-AZ)

- Multi-AZ provides:
  - High availability
  - Automatic failover

👉 For cost reasons:
- Multi-AZ: **Disabled**

---

## Step 7: Auto Failover

- Enables automatic failover if primary node fails

👉 Setting:
- **Auto failover: Enabled**

---

## Step 8: Node Configuration

You can configure:

- Engine version  
- Port  
- Parameter groups  
- Node type  

### Node Type Options

- `t2.micro`
- `t3.micro`
- `t4g.micro`

👉 Free tier options:
- t2.micro / t3.micro

👉 Selected:
- `t2.micro`

---

## Step 9: Replicas

- Replicas improve:
  - Read scalability  
  - High availability (in multi-AZ setups)

👉 For cost reasons:
- Replicas: **0**

---

## Step 10: Subnet Group

- Create subnet group: `my-first-subnet-group`

### Purpose

Defines:
- Which subnets ElastiCache can run in
- VPC association

---

## Step 11: AZ Placement

- Define which nodes go into which Availability Zones

👉 Not important here because:
- Multi-AZ is disabled

---

## Step 12: Encryption Settings

### Encryption at Rest

- Requires AWS KMS key
- Encrypts stored data

### Encryption in Transit

- Encrypts data between client and cache

If enabled:
- Enables access control features

---

## Access Control Options

### 1. Redis AUTH

- Uses password (AUTH token)

### 2. User Group ACL

- Fine-grained access control
- Managed via user groups

👉 In this hands-on:
- Encryption in transit: **Disabled**

---

## Step 13: Security Groups

- Controls network access to ElastiCache
- Defines which applications can connect

---

## Step 14: Backup Settings

- Enable or disable backups
- Used for data recovery

---

## Step 15: Maintenance

- Defines maintenance windows
- Used for:
  - Engine upgrades
  - Patching

---

## Step 16: Logging

- Enable logs such as:
  - Slow logs  
  - Engine logs  

- Can be sent to:
  - **Amazon CloudWatch Logs**

---

## Step 17: Tags

- Used for resource organization and cost tracking

---

## Step 18: Review & Create

- Review all settings
- Click **Create**

---

## Step 19: After Creation

Once created:

- View cluster details
- Access:
  - Nodes
  - Metrics
  - Logs
  - Network security

---

## Connection Info

- Applications connect using:
  - Primary endpoint
  - Reader endpoints (if replicas exist)

👉 Note:
- Actual Redis connection requires application code (not via console directly)

---

## Step 20: Delete Cluster

After testing:

1. Select cluster  
2. Click **Actions → Delete**  
3. Confirm deletion by typing cluster name  
4. Optional: skip final backup  

---

## Summary

This hands-on demonstrated:

- Creating a Redis-based ElastiCache cluster  
- Choosing node-based architecture  
- Configuring cluster mode, replicas, and networking  
- Understanding encryption, security, and backups  
- Using AWS console for full setup lifecycle  
- Deleting the cluster after use  
