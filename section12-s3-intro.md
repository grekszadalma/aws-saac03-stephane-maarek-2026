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

