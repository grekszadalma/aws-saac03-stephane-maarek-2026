
## S3 Lifecycle Rules & Storage Class Transitions

- Objects in Amazon S3 can be moved between storage classes over time.
- Common transitions:
  - Standard → Standard-IA → Intelligent-Tiering → One Zone-IA
  - One Zone-IA → Glacier Flexible Retrieval or Deep Archive
- Choose transitions based on access patterns:
  - Infrequent access → Standard-IA
  - Archival → Glacier or Deep Archive

<img width="605" height="437" alt="image" src="https://github.com/user-attachments/assets/f503fbe7-7b5d-4e3f-82ed-c93fbfc892ae" />

### Lifecycle Rules

Lifecycle rules automate object management and include:

#### 1. Transition Actions
- Move objects to another storage class after a defined time
- Examples:
  - Move to Standard-IA after 60 days
  - Move to Glacier after 6 months

#### 2. Expiration Actions
- Automatically delete objects after a certain time
- Examples:
  - Delete access logs after 365 days
  - Delete old object versions (if versioning enabled)
  - Remove incomplete multipart uploads after a set period (e.g., 2 weeks)

### Rule Scope

- Can apply to:
  - Entire bucket
  - Specific prefix (folder/path)
  - Specific object tags (e.g., department=finance)

---

## Example Scenarios

### Scenario 1: Image Processing Application

- Original images:
  - Stored in Standard (frequent access)
  - Transition to Glacier after 60 days

- Thumbnails:
  - Stored in One Zone-IA (cheap, recreatable)
  - Deleted after 60 days using expiration rule

- Use prefixes to differentiate:
  - `/source/` vs `/thumbnails/`

---

### Scenario 2: Object Recovery Requirements

- Requirement:
  - Recover deleted objects instantly for 30 days
  - Recover within 48 hours for up to 365 days

- Solution:
  - Enable S3 Versioning
  - Deleted objects become hidden via delete markers
  - Lifecycle rules:
    - Transition non-current versions to Standard-IA
    - Then transition to Glacier Deep Archive

---

## S3 Analytics

- Helps determine optimal transition timing
- Provides recommendations for:
  - Standard → Standard-IA transitions
- Does NOT support:
  - One Zone-IA
  - Glacier tiers

### Features:
- Generates daily CSV reports
- Takes 24–48 hours to start producing data
- Useful for optimizing lifecycle policies

---

## Summary

- Lifecycle rules automate storage optimization and cost reduction
- Combine:
  - Transitions (move data)
  - Expiration (delete data)
- Use prefixes and tags for granular control
- Use S3 Analytics to refine strategies

## S3 Requester Pays

- A feature where **the requester (downloader) pays for data transfer costs**, instead of the bucket owner.

### Default Behavior

- By default:
  - **Bucket owner pays** for:
    - Storage
    - Data transfer (downloads)

### Requester Pays Behavior

- When enabled:
  - **Bucket owner still pays for storage**
  - **Requester pays for data transfer (download costs)**

### How It Works

- A user (requester) downloads an object from the bucket
- If Requester Pays is enabled:
  - AWS bills the requester for the network/data transfer cost
- The requester **must be authenticated (not anonymous)** so AWS can bill them

### Use Cases

- Sharing **large datasets** with external users or accounts
- Reducing costs for bucket owners when downloads are frequent or large

### Key Points

- Storage cost → always paid by bucket owner
- Download cost → paid by requester (if enabled)
- Requires authenticated requests (no anonymous access)

### Summary

- Useful for cost-sharing when distributing large amounts of data
- Common in data-sharing platforms or public datasets where users download files
<img width="621" height="453" alt="image" src="https://github.com/user-attachments/assets/fa6719f9-186d-464c-800b-169341c93e7b" />

## S3 Event Notifications

- Allows you to **react automatically to events** occurring in an S3 bucket.

### What Are S3 Events?

Examples of events:
- Object created (upload)
- Object deleted
- Object restored (from Glacier)
- Replication events

- Events can be **filtered**:
  - By prefix (folder/path)
  - By suffix (e.g., `.jpg`, `.png`)

---

## Use Case Example

- Automatically generate thumbnails when images are uploaded:
  - Upload image → trigger event → process image

---

## Event Destinations

S3 can send event notifications to:

1. **SNS (Simple Notification Service)** → pub/sub messaging
2. **SQS (Simple Queue Service)** → message queue
3. **Lambda** → serverless function execution

- You can configure **multiple event notifications** and targets
- Events are typically delivered **within seconds** (can sometimes take longer)

---

## Permissions (Important)

- S3 needs permission to send events to targets
- Use **resource-based policies** (not IAM roles):

- SNS → attach **SNS topic policy**
- SQS → attach **SQS queue policy**
- Lambda → attach **Lambda resource policy**

- These policies allow S3 to:
  - Publish messages (SNS)
  - Send messages (SQS)
  - Invoke functions (Lambda)

---

## EventBridge Integration

- All S3 events can also be sent to **Amazon EventBridge**

### Benefits of EventBridge:
- Advanced filtering:
  - Object name
  - Size
  - Metadata
- Multiple destinations (18+ AWS services)
- Examples:
  - Step Functions
  - Kinesis Data Streams
  - Firehose

### Additional Features:
- Event archiving
- Event replay
- Improved reliability

---

## Summary

- S3 Event Notifications enable **event-driven architectures**
- Core targets:
  - SNS
  - SQS
  - Lambda
  - EventBridge
- Requires **resource-based permissions**
- Useful for automation, processing pipelines, and real-time reactions

# Amazon S3 Performance – Key Notes

## Baseline Performance
- Amazon S3 automatically scales to handle very high request rates.
- Typical latency: ~100–200 ms to retrieve the first byte.
- Performance limits per **prefix**:
  - **3,500 PUT/COPY/POST/DELETE requests per second**
  - **5,500 GET/HEAD requests per second**
- There is **no limit on the number of prefixes**, allowing horizontal scaling.

### What is a Prefix?
- A prefix is the path between the bucket name and the object name.
- Example:
  - `bucket/folder1/subfolder1/file` → prefix = `folder1/subfolder1`
- Each prefix gets its own performance limits.
- Using multiple prefixes increases total throughput.
  - Example: 4 prefixes → up to 22,000 GET/HEAD requests/sec.

## Performance Optimization Techniques

### 1. Multi-Part Upload
- Recommended for files **>100 MB**
- Required for files **>5 GB**
- Splits files into smaller parts and uploads them **in parallel**
- Improves upload speed and maximizes bandwidth usage
- S3 automatically reassembles parts into a single object

### 2. S3 Transfer Acceleration
- Speeds up uploads/downloads over long distances
- Uses **AWS Edge Locations** to reduce latency
- Flow:
  - Client uploads to nearest edge location
  - Data travels via AWS private network to S3 bucket
- Reduces reliance on slower public internet
- Works with multi-part upload

### 3. S3 Byte-Range Fetches
- Retrieves specific portions of an object (byte ranges)
- Enables **parallel downloads** of large files
- Benefits:
  - Faster downloads via parallelization
  - Improved resilience (retry only failed parts)
  - Efficient partial data retrieval (e.g., file headers)

#### Use Cases:
- Download large files in chunks
- Retrieve only needed portions (e.g., first bytes for metadata)

## Summary
- Scale performance using multiple prefixes
- Use multi-part upload for large file uploads
- Use transfer acceleration for global users
- Use byte-range fetches to optimize downloads and partial reads

# Amazon S3 Batch Operations – Key Notes

## Overview
- **S3 Batch Operations** allow you to perform bulk actions on many existing S3 objects with a **single request**.
- Designed for large-scale object management without writing custom scripts.

## Common Use Cases
- Modify object **metadata and properties** in bulk
- Copy large numbers of objects between buckets
- **Encrypt unencrypted objects** (common exam scenario)
- Update **ACLs or object tags**
- Restore multiple objects from **Glacier storage classes**
- Invoke an **AWS Lambda function** for custom processing on each object

## How It Works
A batch job includes:
- A **list of objects**
- The **operation/action** to perform
- Optional **parameters**

## Benefits Over Custom Scripts
- Automatic **retry handling**
- **Progress tracking**
- **Completion notifications**
- Ability to generate **detailed reports**

## Generating the Object List
1. Use **S3 Inventory** to generate a list of objects in a bucket
2. Use **Amazon Athena** to query and filter the list
3. Provide the filtered list to S3 Batch Operations

## Workflow Summary
1. Generate object list using S3 Inventory
2. Filter objects with Athena (e.g., unencrypted objects)
3. Create batch job with:
   - Object list
   - Desired operation (e.g., encryption)
4. S3 Batch processes objects automatically

<img width="428" height="588" alt="image" src="https://github.com/user-attachments/assets/1aa80bfd-0964-4158-866f-bb97c593c486" />

## Key Example (Exam-Relevant)
- Identify unencrypted objects using S3 Inventory
- Use S3 Batch Operations to **encrypt all objects at once**

## Summary
- Best tool for **large-scale bulk operations** in S3
- Eliminates need for manual scripting
- Integrates with S3 Inventory and Athena for object selection
- Supports automation, monitoring, and reporting

# Amazon S3 Storage Lens – Key Notes

## Overview
- **S3 Storage Lens** helps you **understand, analyze, and optimize** storage across your entire AWS Organization.
- Provides visibility into:
  - Usage patterns
  - Cost optimization opportunities
  - Data protection best practices
- Covers multiple scopes:
  - Organization
  - Accounts
  - Regions
  - Buckets
  - Prefixes

## Core Features
- Provides **30-day usage and activity metrics**
- Data can be **aggregated at multiple levels** (org, account, region, bucket, prefix)
- Offers:
  - **Default dashboard** (pre-configured)
  - Ability to create **custom dashboards**
- Metrics and reports can be exported to S3 in:
  - **CSV**
  - **Parquet**

## Default Dashboard
- Automatically enabled and pre-configured
- Displays data across **multiple accounts and regions**
- Cannot be deleted (but can be disabled)
- Provides high-level insights such as:
  - Total storage (bytes)
  - Object count
  - Average object size
  - Number of buckets and accounts
- Supports filtering by:
  - Region
  - Account
  - Bucket
  - Storage class

## Metrics Categories

### 1. Summary Metrics
- General insights:
  - Total storage size
  - Object count
- Use cases:
  - Identify growing or inactive buckets/prefixes

### 2. Cost Optimization Metrics
- Insights into storage cost efficiency:
  - Non-current version storage size
  - Incomplete multipart upload storage
- Use cases:
  - Detect unused storage
  - Identify candidates for cheaper storage classes

### 3. Data Protection Metrics
- Tracks protection features:
  - Versioning-enabled buckets
  - MFA delete enabled buckets
  - SSE-KMS encryption usage
  - Replication configuration
- Use cases:
  - Ensure compliance with security best practices

### 4. Access Management Metrics
- Insights into object ownership and permissions
- Use cases:
  - Audit ownership configurations

### 5. Event Metrics
- Tracks S3 Event Notifications usage
- Use cases:
  - Identify buckets configured for event-driven workflows

### 6. Performance Metrics
- Insights into features like **Transfer Acceleration**
- Use cases:
  - Monitor performance-related configurations

### 7. Activity Metrics
- Tracks request activity:
  - GET, PUT requests
  - Data transferred (bytes downloaded/uploaded)

### 8. HTTP Status Code Metrics
- Tracks response codes:
  - 200 (OK)
  - 403 (Forbidden)
- Use cases:
  - Understand access patterns and errors

## Free vs Advanced Metrics

### Free Metrics
- Available by default
- ~28 usage metrics
- Data retention: **14 days**
- Includes basic insights

### Advanced (Paid) Metrics
- Additional features:
  - Activity metrics
  - Advanced cost optimization
  - Advanced data protection
  - HTTP status codes
- Benefits:
  - Metrics available in **Amazon CloudWatch**
  - **Prefix-level analysis**
  - Data retention: **15 months**

## Key Takeaways
- Centralized visibility across **entire AWS Organization**
- Helps optimize:
  - Cost
  - Security
  - Performance
- Default dashboard is multi-account and multi-region
- Advanced metrics provide deeper insights and longer retention

Questions:

Question 1:

How can you be notified when there's an object uploaded to your S3 bucket?
- S3 Event Notification

Question 2:

You have an S3 bucket that has S3 Versioning enabled. This S3 bucket has a lot of objects, and you would like to remove old object versions to reduce costs. 
What's the best approach to automate the deletion of these old object versions?
- S3 Lifecycle Rules - Expiration Actions

Question 3:

How can you automate the transition of S3 objects between their different tiers?
- S3 Lifecycle Rules

Question 4:

While you're uploading large files to an S3 bucket using Multi-part Upload, there are a lot of unfinished parts stored in the S3 bucket due to network issues. You are not using these unfinished parts and they cost you money.
What is the best approach to remove these unfinished parts?
- S3 Lifecycle Policy to automate deletion

Question 5:

You are looking to get recommendations for S3 Lifecycle Rules. How can you analyze the optimal number of days to move objects between different storage tiers?
- S3 Analytics

Question 6:

You are looking to build an index of your files in S3, using Amazon RDS PostgreSQL. To build this index, it is necessary to read the first 250 bytes of each object in S3, which contains some metadata about the content of the file itself. There are over 100,000 files in your S3 bucket, amounting to 50 TB of data. How can you build this index efficiently?
- Create an app to go through the S3 Bucket, issue a Byte Range Fetch to get the first 250 bytes than store them in RDS

Question 7:

You have a large dataset stored on-premises that you want to upload to the S3 bucket. The dataset is divided into 10 GB files. You have good bandwidth but your Internet connection isn't stable. What is the best way to upload this dataset to S3 and ensure that the process is fast and avoid any problems with the Internet connection?
- use S3 multi-part upload and transfer acceleration

Question 8:

A company is preparing for compliance and regulatory review on its infrastructure on AWS. Currently, they have their files stored on S3 buckets encrypted using S3 Default Encryption, which must be encrypted using KMS as required for compliance and regulatory review. Which S3 feature allows them to encrypt all files in their S3 buckets in the most efficient and cost-effective way?
- S3 Batch Operation

Question 9:

You have a 25 GB file that you're trying to upload to S3 but you're getting errors. What is a possible solution for this?
- Use multi part upload when we want to upload a file that is bigger than 5GB

