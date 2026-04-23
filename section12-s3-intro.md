## Amazon S3 Overview

Amazon S3 is a core AWS service that provides infinitely scalable object storage. It is widely used as a foundational component for many applications and AWS services.

## Common Use Cases

- Backup and storage (files, disks, general data)
- Disaster recovery (replicating data across regions)
- Archival storage (long-term, cost-efficient storage with S3 Glacier)
- Hybrid cloud storage (extending on-premises storage to the cloud)
- Hosting applications and static websites
- Storing and serving media files (images, videos)
- Data lakes and big data analytics
- Software distribution and updates

Real-world examples:
- NASDAQ stores long-term data in S3 Glacier
- Sysco uses S3 for analytics and business insights

## Buckets

- Buckets are containers for storing objects (similar to directories)
- Each bucket is created in a specific AWS region
- S3 has a global interface, but buckets themselves are region-scoped

### Naming Rules

- Must be globally unique (historically), but now AWS supports account-based regional namespaces with automatic uniqueness suffixes
- No uppercase letters or underscores
- Cannot look like an IP address
- Must start with a lowercase letter or number
- Cannot start with "xn--"
- Cannot end with "-s3alias"
- Best practice: use simple lowercase alphanumeric names

## Objects

- Objects are the actual files stored in S3
- Each object is identified by a key (its full path)

### Object Key Structure

- The key represents the full path:
  - Example: `my_folder/another_folder/my_file.txt`
- Key = prefix + object name
  - Prefix: folder-like path (`my_folder/another_folder/`)
  - Object name: file name (`my_file.txt`)
- S3 does not truly have directories; folders are just part of the key

## Object Details

- Object content = actual file data
- Maximum object size: 5 TB

### Multipart Upload

- Required for files larger than 5 GB
- File is split into parts and uploaded in parallel
- Example: a 5 TB file must be split into at least 1,000 parts

## Metadata and Tags

- Metadata: key-value pairs describing the object (system-defined or user-defined)
- Tags:
  - Up to 10 key-value pairs per object
  - Useful for security, cost allocation, and lifecycle rules

## Versioning

- Objects can have version IDs if versioning is enabled
- Allows tracking changes and restoring previous versions

## Amazon S3 Security Overview

Amazon S3 security is managed through multiple layers, primarily using IAM policies, bucket policies, ACLs, and encryption.

## 1. User-Based Security (IAM Policies)

- IAM policies are attached to users, groups, or roles
- Define which S3 API actions are allowed or denied
- Example actions:
  - GetObject
  - PutObject
  - DeleteObject
- Controls access from within your AWS account

## 2. Resource-Based Security (Bucket Policies)

- S3 Bucket Policies are JSON-based policies attached directly to buckets
- Apply rules at the bucket level
- Common use cases:
  - Grant public access
  - Enable cross-account access
  - Enforce encryption requirements

### Structure of a Bucket Policy

- Resource:
  - Specifies bucket and objects (e.g., `arn:aws:s3:::example-bucket/*`)
- Effect:
  - Allow or Deny
- Action:
  - API operations (e.g., `s3:GetObject`)
- Principal:
  - Who the policy applies to (e.g., `*` = everyone)

### Example Behavior

<img width="492" height="412" alt="image" src="https://github.com/user-attachments/assets/7f4521d2-dc6c-477e-96cd-41e3bc88af89" />

- Allow `s3:GetObject` for all users (`Principal: *`)
- Result: all objects in the bucket are publicly readable

## 3. Access Control Lists (ACLs)

- Object ACL:
  - Fine-grained permissions at object level
  - Can be disabled (recommended)
- Bucket ACL:
  - Less commonly used
- Modern best practice:
  - Disable ACLs and rely on IAM + bucket policies

## 4. Access Evaluation Logic

An IAM principal can access an S3 object if:
- IAM policy allows it OR bucket policy allows it
- AND there is no explicit deny in any policy

## 5. Encryption

- Objects can be encrypted using:
  - S3-managed keys (SSE-S3)
  - Other encryption methods (covered later)
- Used as an additional security layer

## 6. Common Access Scenarios

### Public Access

- Achieved using a bucket policy with:
  - Principal: `*`
  - Action: `GetObject`
- Allows anyone on the internet to read objects

### IAM User Access (Same Account)

- IAM policy grants permissions to access S3
- No bucket policy required if IAM allows access

### EC2 Access

- Use IAM Roles (not IAM users)
- Attach role to EC2 instance
- Role grants permissions to access S3

### Cross-Account Access

- Requires bucket policy
- Grants access to IAM users from another AWS account

## 7. Block Public Access Settings

- Additional safety layer at bucket or account level
- Prevents accidental public exposure

### Key Points

- If enabled:
  - Overrides bucket policies that allow public access
  - Bucket will remain private
- Recommended:
  - Keep enabled unless public access is explicitly required
- Can be enforced at:
  - Bucket level
  - Account level

## Key Takeaways

- Use IAM policies for user/role permissions
- Use bucket policies for public or cross-account access
- Disable ACLs for simplicity and security
- Block Public Access is a critical safeguard
- Access is granted only if allowed and not explicitly denied
- Encryption adds an extra layer of protection

## Hosting Static Websites with Amazon S3

Amazon S3 can be used to host static websites (HTML, CSS, JavaScript, images) and make them accessible over the internet.

## How It Works

- Create an S3 bucket
- Upload static website files (e.g., `index.html`, images)
- Enable **Static Website Hosting** on the bucket
- Users access the website via a public S3 endpoint URL

## Website Endpoint URL

- The URL depends on the AWS region of the bucket
- Common formats:
  - `http://bucket-name.s3-website-<region>.amazonaws.com`
  - `http://bucket-name.s3-website.<region>.amazonaws.com`
- Difference is minor (dash vs dot) and not critical to memorize

## Requirements for Public Access

- S3 must be able to serve files publicly
- This requires:
  1. Disabling **Block Public Access**
  2. Adding a **Bucket Policy** to allow public reads

### Required Permission

- `s3:GetObject` must be allowed for:
  - `Principal: "*"` (anyone)
  - Resource: `bucket-name/*`

## Common Issue: 403 Forbidden

- If you see a **403 Forbidden** error:
  - The bucket or objects are not publicly accessible
  - Likely causes:
    - Block Public Access still enabled
    - Missing or incorrect bucket policy

## Summary

- S3 can host static websites easily and cheaply
- Files are served directly from the bucket
- Public access must be explicitly enabled
- Bucket policies are required for public website access

## Amazon S3 Versioning

Amazon S3 versioning allows you to keep multiple versions of an object within the same bucket. It is enabled at the bucket level and is a best practice for protecting data.

### How Versioning Works
- When versioning is enabled on a bucket:
  - Uploading a file creates **version 1** of that object.
  - Uploading a new file with the same key does **not overwrite** the existing file.
  - Instead, it creates a **new version** (version 2, version 3, etc.).
- Each object is identified by:
  - A **key** (file path/name)
  - A **version ID**

### Benefits of Versioning
- **Protection against accidental deletes**
  - Deleting an object does not permanently remove it.
  - Instead, a **delete marker** is added.
  - Previous versions can still be restored.

- **Easy rollback**
  - You can revert to any previous version of a file.
  - Useful for recovering from mistakes or restoring older content.

### Important Notes
- Objects uploaded **before enabling versioning**:
  - Have a version ID of `null`.
- **Suspending versioning**:
  - Does **not delete existing versions**.
  - Previous versions remain accessible.
- Versioning must be explicitly enabled per bucket.

### Summary
Versioning in S3 provides a simple and powerful way to:
- Preserve file history
- Recover from accidental changes or deletions
- Safely update and manage objects over time

## Amazon S3 Replication (CRR vs SRR)

Amazon S3 Replication allows you to automatically copy objects from one S3 bucket to another in an **asynchronous** manner.

---

### Types of Replication

#### Cross-Region Replication (CRR)
- Replicates objects between buckets in **different AWS regions**
- Example: us-east-1 → eu-west-1
- Common use cases:
  - Compliance requirements (data stored in multiple regions)
  - Lower latency access for global users
  - Disaster recovery across regions
  - Cross-region data sharing

---

#### Same-Region Replication (SRR)
- Replicates objects between buckets in the **same AWS region**
- Example: eu-west-1 → eu-west-1
- Common use cases:
  - Aggregating logs from multiple buckets
  - Splitting production and test data within the same region
  - Data synchronization between environments

---

### Key Requirements
- **Versioning must be enabled**
  - Both source bucket and destination bucket must have versioning enabled
- Replication is **asynchronous**
  - Objects are not copied instantly; they replicate in the background
- Can work across:
  - Different AWS accounts
  - Same AWS account

---

### IAM Permissions Requirement
To enable replication:
- You must provide an **IAM role**
- This role allows S3 to:
  - Read objects from the source bucket
  - Write objects into the destination bucket

Without proper permissions, replication will fail.

---

### How It Works (Conceptually)
1. Object is uploaded to source bucket
2. S3 detects the change (new version created)
3. Replication service copies the object asynchronously
4. Object appears in destination bucket

---

### Summary
- CRR = replication across regions (global use cases)
- SRR = replication within same region (internal workflows)
- Requires versioning + IAM role
- Works asynchronously in the background

## Amazon S3 Replication – Additional Notes

### Replication Scope
- After enabling replication:
  - **Only new objects** are replicated automatically
- Existing objects in the bucket are **not replicated by default**

---

### Replicating Existing Objects
- Use **S3 Batch Replication** to:
  - Replicate existing objects
  - Retry replication for objects that previously failed

---

### Delete Marker Replication
- You can optionally enable replication of **delete markers**
  - When enabled:
    - Deletions in source bucket are reflected in destination bucket

---

### Permanent Deletions
- If an object is deleted using a **specific version ID (permanent delete)**:
  - This deletion is **NOT replicated**
- Purpose:
  - Prevent accidental or malicious permanent deletions from propagating

---

### No Replication Chaining
- Replication does **not cascade across buckets**

Example:
- Bucket A → replicates to Bucket B
- Bucket B → replicates to Bucket C

Result:
- Objects from **Bucket A are NOT replicated to Bucket C**

---

### Key Takeaways
- Replication applies only to new objects unless using Batch Replication
- Delete marker replication is optional
- Permanent deletes are not replicated for safety
- Replication does not chain across multiple buckets

<img width="1195" height="492" alt="image" src="https://github.com/user-attachments/assets/ac60a14c-f990-43ae-9aa8-492a2c54ffda" />

<img width="1217" height="431" alt="image" src="https://github.com/user-attachments/assets/663e74a4-c28a-4c71-9304-ca7043830ee5" />

