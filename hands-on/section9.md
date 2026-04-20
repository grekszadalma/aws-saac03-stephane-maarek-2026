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
