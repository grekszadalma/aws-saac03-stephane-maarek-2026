# Amazon Route 53 – Hands-On (Domain Registration)

## Overview

- In this hands-on, we:
  - Register a domain using Route 53
  - Understand hosted zones
  - Verify DNS setup

⚠️ Important:
- Registering a domain costs money (~$12–$13/year)
- If you don’t want to pay → just watch, don’t perform

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

