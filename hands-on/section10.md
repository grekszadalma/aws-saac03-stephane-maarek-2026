# Amazon Route 53 – Hands-On (Domain Registration)

## Overview

- In this hands-on, we:
  - Register a domain using Route 53
  - Understand hosted zones
  - Verify DNS setup

⚠️ Important:
- Registering a domain costs money (~$12–$13/year)

---

## Step 1: Access Route 53

- Go to Route 53 console
- On the left sidebar:
  - Click "Registered domains"

---

## Step 2: Register a Domain

1. Click "Register domain"
2. Enter a domain name
   - Example: myuniquedomain123.com
   - Must be globally unique
3. Check availability
4. Add to cart if available

Example:
- myuniquedomain123.com → $13/year

---

## Step 3: Checkout

- Review selected domain
- Choose:
  - Duration (e.g., 1 year)
  - Auto-renew

### Auto-renew Recommendation

- Enable if you want to keep the domain
- Disable if only using for practice

⚠️ If disabled:
- You may lose the domain
- Someone else can buy it

---

## Step 4: Contact Information

- Fill in or confirm:
  - Registrant contact
  - Admin contact
  - Technical contact

### Privacy Protection

- Enable privacy protection
  - Hides your personal info (email, phone, address)
  - Prevents spam

---

## Step 5: Review & Purchase

- Review all details
- Accept terms & conditions
- Click Submit

⚠️ This will charge you (~$13)

---

## Step 6: Wait for Registration

- Takes:
  - A few minutes → up to a few hours
- Once done → domain is active

---

## Step 7: Verify Hosted Zone

1. Go to Hosted Zones
2. Click your domain  
   Example:
   - myuniquedomain123.com

---

## Default Records Created

You should see 2 default records:

### NS Record (Name Servers)

Example:
- myuniquedomain123.com → ns-xxx.awsdns-xx.com

Purpose:
- Points domain to AWS Route 53 DNS servers
- Route 53 becomes the source of truth

---

### SOA Record (Start of Authority)

- Contains:
  - DNS configuration info
  - Authority details for the domain

---

## Important Concept

- Once domain is registered:
  - Route 53 = authoritative DNS
  - Any DNS record you create (A, CNAME, etc.) is managed inside Route 53

---

## What This Means

- When users query:
  - myuniquedomain123.com
- Route 53 answers with:
  - IP addresses
  - or other DNS records

---

## Summary

- Register domain via Route 53 (~$12/year)
- Enable privacy protection
- Hosted Zone is automatically created
- Default records:
  - NS (points to AWS DNS)
  - SOA (domain authority info)
- Route 53 becomes DNS source of truth

---
# Route 53 Hands-On – Creating Your First DNS Record

## Create a Record in Hosted Zone

1. Go to your **Hosted Zone** in Route 53  
2. Click on **Create Record**

### Record Configuration

- **Record Name**:  
  Example: `test.stephanetheteacher.com`  
  (You can enter any subdomain you want)

- **Record Type**:  
  `A Record` → maps a domain name to an IPv4 address

- **Value (IP Address)**:  
  `11.22.33.44`  
  (Example IP, not necessarily a real server)

- **TTL (Time To Live)**:  
  `300 seconds` (default)

- **Routing Policy**:  
  `Simple routing` (default)

👉 Click **Create Record**

---

## What Happens Behind the Scenes

When a client requests:
test.stephanetheteacher.com

Route 53 responds with:

11.22.33.44

So the DNS resolution works even if no real server exists at that IP.

---

## Testing DNS Resolution

### Using a Web Browser

- Enter: `test.stephanetheteacher.com`  
- Result: ❌ Likely won’t work  
  (because no actual server is running at that IP)

---

## Using Command Line Tools

### Option 1: AWS CloudShell

1. Open **CloudShell** in AWS Console  
2. Install DNS tools:

```bash
sudo yum install -y bind-utils
```

Using nslookup
```bash
nslookup test.stephanetheteacher.com
```

Output:
```bash
Name: test.stephanetheteacher.com
Address: 11.22.33.44
```

Using dig:
```bash
dig test.stephanetheteacher.com
```

Output:
```bash
ANSWER SECTION:
test.stephanetheteacher.com. 300 IN A 11.22.33.44
```

Key Points:
Record type: A
Value: 11.22.33.44
TTL: 300 seconds

# Route 53 Hands-On – EC2 Instances Across Regions + ALB Setup

## Goal of This Lab

Before using Route 53, we:
- Launch **3 EC2 instances in 3 different AWS regions**
- Deploy a **User Data script**
- Create an **Application Load Balancer (ALB)**
- Test connectivity via public IPs and ALB DNS name

---

## 1. Launch EC2 Instance – Frankfurt (eu-central-1)

### Configuration

- **AMI**: Amazon Linux 2  
- **Instance Type**: t2.micro  
- **Key Pair**: None (use EC2 Instance Connect if needed)

### Security Group

- Allow **SSH (22)** from anywhere  
- Allow **HTTP (80)** from anywhere  

---

### User Data Script

Paste EC2 bootstrap script:

- Prints `Hello World`
- Displays **Availability Zone** using:
  - `EC2_AVAILABILITY_ZONE`

---

### Result

- Instance launched in **Frankfurt**
- Public IP accessible via HTTP

Example output:
Hello from EU-CENTRAL-1B


---

## 2. Launch EC2 Instance – Northern Virginia (us-east-1)

### Configuration

- Same settings as before:
  - Amazon Linux 2
  - t2.micro
  - No key pair
  - HTTP + SSH enabled

### User Data

- Same bootstrap script reused

---

### Result

Example output: Hello from US-EAST-1A


---

## 3. Launch EC2 Instance – Singapore (ap-southeast-1)

### Configuration

- Same setup again:
  - Amazon Linux 2
  - t2.micro
  - No key pair
  - HTTP enabled

### User Data

- Same script reused

---

### Result

Example output: Hello from AP-SOUTHEAST-1B


---

## 4. Summary of EC2 Instances

| Region | AZ | Status |
|--------|----|--------|
| Frankfurt | eu-central-1b | Running |
| N. Virginia | us-east-1a | Running |
| Singapore | ap-southeast-1b | Running |

---

## 5. Create Application Load Balancer (Frankfurt)

### Configuration

- **Type**: Application Load Balancer (ALB)
- **Name**: `DemoRoute53ALB`
- **Scheme**: Internet-facing
- **IP Type**: IPv4

---

### Networking

- Select **multiple subnets (1, 2, 3 AZs)**
- Attach existing **Security Group**:
  - Allows HTTP (80)
  - Allows required access

---

## 6. Target Group Setup

### Target Group Name
demo-tg-route-53


### Type

- Based on **Instances**

### Register Targets

- Add EC2 instance(s)
- Click **Include as pending below**
- Create target group

---

## 7. Attach Target Group to ALB

- Select target group: `demo-tg-route-53`
- Forward traffic on **port 80**
- Create Load Balancer

---

## 8. Testing EC2 Instances

### Test each instance via browser:


http://<public-ip>

Output:
### Frankfurt
Hello from EU-CENTRAL-1B
### US East
Hello from US-EAST-1A
### Singapore
Hello from AP-SOUTHEAST-1B


---

## 9. Record Instance Info

Example log:

- Frankfurt → EU-CENTRAL-1B  
- US East → US-EAST-1A  
- Singapore → AP-SOUTHEAST-1B  

---

## 10. Test Load Balancer

### Get ALB DNS Name

- Copy ALB DNS from AWS console
- Open in browser

Example: https://demoroute53alb-xxxx.amazonaws.com/

Result: Because it's pointing to one of the instances, central-1b.
