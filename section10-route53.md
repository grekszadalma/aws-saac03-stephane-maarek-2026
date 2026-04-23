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

# Domain Registrar vs DNS Service (Route 53 Notes)

It is important to understand the difference between a domain registrar and a DNS service.

A domain registrar is where you purchase your domain name (for example, example.com). This usually involves paying an annual fee to keep ownership of the domain. Examples of registrars include AWS (via Route 53), GoDaddy, and Google Domains.

A DNS service, on the other hand, is responsible for managing DNS records for your domain. These records define how domain names are translated into IP addresses or other resources.

When you register a domain with a registrar, they often provide a built-in DNS service. For example, when registering a domain through AWS, a Route 53 hosted zone is automatically created to manage DNS records.

However, the registrar and DNS service do not have to be the same provider. You can mix and match them:

- You can register a domain with AWS and use a different DNS provider.
- You can register a domain with a third-party registrar (such as GoDaddy) and use Route 53 as your DNS service.

To use Route 53 as the DNS service for a domain registered elsewhere, follow these steps:

1. Create a public hosted zone in Route 53 for your domain.
2. Route 53 will provide a set of name servers (NS records).
3. Go to your domain registrar’s website.
4. Update the domain’s name server settings to use the Route 53 name servers.

Once this is done, all DNS queries for your domain will be handled by Route 53, even though the domain itself was purchased from a different registrar.

In summary:
- The domain registrar is where you buy and renew your domain name.
- The DNS service is where you manage DNS records.
- These two roles can be handled by the same provider or by different providers.
- To use Route 53 with a third-party registrar, you must update the domain’s name servers to point to Route 53.
<img width="1134" height="488" alt="image" src="https://github.com/user-attachments/assets/6c5671cc-b1b1-4402-b67a-96e8da2d0112" />

# Route 53 Resolver (Notes)

The Route 53 Resolver is the default DNS resolver in AWS. It automatically answers DNS queries for resources within your AWS environment.

By default, the Route 53 Resolver can resolve:
- DNS names of EC2 instances
- Records in private hosted zones
- Records in public hosted zones

This means that anything you configure inside Route 53 is resolvable within your AWS account.

However, in real-world scenarios, you may need a hybrid DNS setup. This happens when you want DNS resolution to work between your AWS environment and your on-premises infrastructure (for example, a corporate data center).

To enable this, you use Route 53 Resolver endpoints, which allow DNS queries to flow between AWS and external systems.

## Inbound Endpoint (On-Premises → AWS)

An inbound endpoint allows DNS queries from your on-premises network to resolve AWS domain names.

How it works:
- You establish network connectivity between on-premises and AWS (via VPN or Direct Connect).
- A server in your on-premises network makes a DNS query (for example, for a private hosted zone in AWS).
- The query goes to the on-premises DNS resolver.
- That resolver forwards the query to the Route 53 inbound endpoint.
- The inbound endpoint passes the query to the Route 53 Resolver.
- The correct DNS response is returned back to the on-premises system.

This allows on-premises systems to resolve AWS private resources.

## Outbound Endpoint (AWS → On-Premises)

An outbound endpoint allows AWS resources to resolve domain names hosted in your on-premises environment.

How it works:
- An EC2 instance (or other AWS resource) makes a DNS query for an on-premises domain (for example, web.onpremise.local).
- The Route 53 Resolver forwards this query to the outbound endpoint.
- The outbound endpoint sends the query to the on-premises DNS resolver.
- The on-premises resolver responds with the correct IP address.

This allows AWS resources to resolve internal company domains.

## Key Concept

- Inbound endpoint: lets on-premises systems resolve AWS DNS.
- Outbound endpoint: lets AWS systems resolve on-premises DNS.
- Both require network connectivity (VPN or Direct Connect).

## Summary

The Route 53 Resolver enables DNS resolution within AWS by default. To extend this capability to hybrid environments (AWS + on-premises), you must configure inbound and outbound resolver endpoints. This setup ensures DNS queries can flow in both directions between AWS and external networks.

## Questions

Question 1:

You have purchased mycoolcompany.com on Amazon Route 53 Registrar and would like the domain to point to your Elastic Load Balancer my-elb-1234567890.us-west-2.elb.amazonaws.com. Which Route 53 Record type must you use here?
- CNAME
- Alias (correct)

Question 2:

You have deployed a new Elastic Beanstalk environment and would like to direct 5% of your production traffic to this new environment. This allows you to monitor for CloudWatch metrics and ensuring that there're no bugs exist with your new environment. Which Route 53 Routing Policy allows you to do so?
- Weighted

Question 3:

You have updated a Route 53 Record's myapp.mydomain.com value to point to a new Elastic Load Balancer, but it looks like users are still redirected to the old ELB. What is a possible cause for this behavior?
- Because of the TTL

Question 4:

You have an application that's hosted in two different AWS Regions us-west-1 and eu-west-2. You want your users to get the best possible user experience by minimizing the response time from application servers to your users. Which Route 53 Routing Policy should you choose?
- Latency

Question 5:

You have a legal requirement that people in any country but France should NOT be able to access your website. Which Route 53 Routing Policy helps you in achieving this?
- Geolocation

Question 6:

You have purchased a domain on GoDaddy and would like to use Route 53 as the DNS Service Provider. What should you do to make this work?
- Create a Public Hosted Zone and update the 3rd party Registrar NS records

Question 7:

Which of the following are NOT valid Route 53 Health Checks?
- Health checks that monitor SQS Queue (this one)
- Health Check that monitors other Health Checks
- Health checks that monitor Cloudwatch alarms
- Health check that monitors an Endpoint
