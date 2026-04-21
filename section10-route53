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
