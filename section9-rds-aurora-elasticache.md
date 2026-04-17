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


