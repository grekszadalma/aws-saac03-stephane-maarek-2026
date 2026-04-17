# Amazon RDS Overview

## Introduction

Let’s get started with an overview of AWS RDS.

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

### Benefits

- Eliminates the need for manual storage scaling  
- Avoids downtime for storage upgrades  
- Ideal for applications with **unpredictable workloads**  
- Supported across all RDS database engines  

---

## Conclusion

Amazon RDS simplifies database management by automating operational tasks and providing scalability, availability, and performance enhancements. It is a powerful alternative to self-managed databases on EC2, especially for production workloads.

<img width="460" height="737" alt="image" src="https://github.com/user-attachments/assets/ba1e3670-73f7-4eb4-b78e-25376586f911" />
