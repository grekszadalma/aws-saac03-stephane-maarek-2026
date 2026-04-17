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
