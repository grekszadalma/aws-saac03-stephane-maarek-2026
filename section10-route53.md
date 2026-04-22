DNS

# Amazon Route 53 – Notes

## Overview

- **Amazon Route 53** is a:
  - Highly available
  - Scalable
  - Fully managed
  - **Authoritative DNS service**

### What does *authoritative* mean?
- You (the customer) have **full control over DNS records**
- You can update DNS entries yourself

---

## Basic Concept

- Clients want to access a domain (e.g., `example.com`)
- Your EC2 instance only has a **public IP**

### How it works:
1. Create DNS records in **Route 53**
2. Store them in a **Hosted Zone**
3. Route 53 resolves `example.com → 54.22.33.44`
4. Clients connect to the EC2 instance

---

## Key Features

- **Domain Registrar**
  - Can register domains like `example.com`

- **Health Checks**
  - Monitor resources

- **100% Availability SLA**
  - Only AWS service with this guarantee

### Why "Route 53"?
- Named after **DNS port 53**

---

## DNS Records

Each record contains:

- **Domain / Subdomain** (e.g., `example.com`, `www.example.com`)
- **Record Type** (A, AAAA, CNAME, NS)
- **Value** (e.g., `12.34.56.78`)
- **Routing Policy**
- **TTL (Time To Live)**

---

## Important Record Types (Exam ⚠️)

### A Record
- Maps hostname → IPv4

Example:
- `example.com → 1.2.3.4`

---

### AAAA Record
- Maps hostname → IPv6

Example:
- `example.com → 2001:0db8:85a3::8a2e:0370:7334`

---

### CNAME Record
- Maps hostname → another hostname

Example:
- `www.example.com → example.com`

⚠️ **Exam Rule**
- Cannot create CNAME at root domain:
  - ❌ `example.com`
  - ✅ `www.example.com`

---

### NS Record
- Defines name servers for the domain
- Controls DNS routing

---

## Hosted Zones

### Definition
- Container for DNS records
- Defines routing for domain and subdomains

---

## Types of Hosted Zones

### Public Hosted Zone

- Used for **public domains**
- Accessible from the **internet**

Example:
- `application1.mypublicdomain.com → public IP`

---

### Private Hosted Zone

- Used for **internal domains**
- Only accessible **inside a VPC**

Example:
- `application1.company.internal`

### Use Case

Internal communication between services:

- `webapp.example.internal → EC2 instance`
- `api.example.internal → EC2 instance`
- `database.example.internal → database`

---

## Public vs Private Hosted Zones

| Feature | Public Hosted Zone | Private Hosted Zone |
|--------|------------------|-------------------|
| Access | Internet | VPC only |
| Use Case | Websites | Internal services |
| Example | example.com | company.internal |

---

## Pricing

- Hosted Zone: ~$0.50/month
- Domain registration: ~$12/year minimum

⚠️ Route 53 is **NOT free**

---

## Summary (Exam Tips ⚠️)

- Route 53 = **Authoritative DNS**
- A = IPv4
- AAAA = IPv6
- CNAME = hostname → hostname
- NS = name servers
- Public Hosted Zone = internet
- Private Hosted Zone = VPC only
- ❗ No CNAME at root domain
- ⭐ 100% SLA (unique in AWS)

# Route 53 – Health Checks Notes

## Overview
- Route 53 Health Checks monitor the **health of resources** and enable **automatic DNS failover**.
- Typically used in **multi-region architectures** to ensure traffic is only routed to healthy endpoints.

---

## Example Architecture
- Two Load Balancers in different regions (e.g., `us-east-1` and `eu-west-1`)
- Route 53 routes users based on a routing policy (e.g., latency-based)
- Health checks ensure:
  - If one region is **down**, users are **not routed there**
  - Traffic is automatically redirected to **healthy resources**

---

## Types of Health Checks

### 1. Endpoint Health Check (Public Resources)
- Monitors a **public endpoint** (ALB, EC2, API, etc.)
- Performed by ~**15 global health checkers**
- Health checkers send requests to the endpoint

#### Key Details:
- Protocols supported:
  - HTTP
  - HTTPS
  - TCP
- Success criteria:
  - Must return **2xx or 3xx status code**
- Health threshold:
  - If **>18% of health checkers** report healthy → endpoint is **healthy**
  - Otherwise → **unhealthy**
- Interval options:
  - **30 seconds** (standard)
  - **10 seconds** (fast, higher cost)

#### Advanced Feature:
- Can inspect **first 5,120 bytes** of response for specific text

#### Important:
- Must allow incoming traffic from **Route 53 health checker IP ranges**
- Otherwise health checks will fail

---

### 2. Calculated Health Check
- Combines multiple health checks into one **parent health check**

#### Structure:
- Multiple **child health checks** (e.g., per EC2 instance)
- One **parent health check**

#### Logic Options:
- AND → all must pass
- OR → at least one must pass
- NOT → invert result

#### Capabilities:
- Up to **256 child health checks**
- Can define how many must pass

#### Use Case:
- Maintain availability during **partial outages or maintenance**
- Avoid marking entire system unhealthy if only part fails

---

### 3. CloudWatch Alarm Health Check (Private Resources)
- Used for **private resources** (inside VPC or on-prem)

#### Problem:
- Route 53 health checkers are **public**
- Cannot directly access private endpoints

#### Solution:
1. Create a **CloudWatch Metric**
2. Create a **CloudWatch Alarm** based on that metric
3. Link the alarm to a **Route 53 Health Check**

#### Behavior:
- If alarm is **OK → healthy**
- If alarm is **ALARM → unhealthy**

#### Common Use Case:
- Monitor:
  - EC2 in private subnet
  - Internal services
  - On-prem infrastructure

---

## Integration with Route 53
- Health checks are **associated with DNS records**
- Used with routing policies (e.g., failover, latency)

### Result:
- Route 53 only returns **healthy endpoints**
- Enables **automatic failover**

---

## Monitoring
- Health checks publish metrics to **CloudWatch**
- Can be visualized and alerted on

---

## Key Takeaways
- Health checks improve **availability and resilience**
- Three main types:
  - Endpoint (public)
  - Calculated (composite)
  - CloudWatch (private)
- Require proper **network access configuration**
- Critical for **failover routing policies**
