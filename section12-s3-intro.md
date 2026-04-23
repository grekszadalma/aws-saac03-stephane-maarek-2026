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
