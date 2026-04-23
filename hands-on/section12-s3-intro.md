## Creating and Managing an Amazon S3 Bucket

### Bucket Creation

- Buckets are created in a specific AWS region (e.g., eu-west-1)
- Two bucket types:
  - General purpose (default and most common)
  - Directory (for low-latency use cases, less common)

### Bucket Naming and Namespace

- Two namespace options:
  - Global namespace:
    - Bucket names must be globally unique across all AWS accounts
    - If a name already exists, you must choose another
  - Account regional namespace:
    - Allows reuse of bucket names across regions and accounts
    - AWS appends a unique suffix (account ID + region)
    - Recommended approach going forward

### Bucket Configuration (Typical Defaults)

- Object Ownership: ACLs disabled (recommended for security)
- Block Public Access: enabled (prevents public access by default)
- Bucket Versioning: disabled initially (can be enabled later)
- Tags: optional
- Default Encryption:
  - Server-side encryption using S3-managed keys (SSE-S3)
  - Bucket Key can be enabled for cost optimization

### Viewing Buckets

- All buckets across all regions are visible in the S3 console
- Can search and filter buckets easily

## Uploading Objects

- Inside a bucket, you can upload files (objects)
- Example:
  - Upload `coffee.jpg` (~100 KB)
- Objects appear in the "Objects" tab

## Object Details

- Each object shows:
  - Size, type, region
  - Object URL (public URL)
  - Metadata and properties

## Accessing Objects

### Pre-Signed URL (Works)

- Generated URL includes authentication signature
- Contains user credentials embedded in the request
- Allows temporary authorized access to private objects

### Public URL (Fails by Default)

- Returns `AccessDenied` if:
  - Bucket blocks public access
  - Object is not explicitly made public

- By default:
  - Objects are private
  - Only accessible via authenticated requests (e.g., pre-signed URLs)

## Folder Structure in S3

- You can create folders (e.g., `images/`)
- Folders are just prefixes in object keys (not real directories)

### Example

- Upload `beach.jpg` into `images/`
- Object key becomes:
  - `images/beach.jpg`

## Managing Objects and Folders

- Can:
  - Upload files into folders
  - Navigate folders like a file system
  - Delete objects or entire folders

- Deleting a folder:
  - Deletes all objects inside it
  - Requires confirmation (type "permanently delete")

## Key Takeaways

- Buckets are region-specific but globally visible
- Account regional namespace simplifies naming
- Objects are private by default
- Pre-signed URLs allow secure temporary access
- "Folders" are logical (prefix-based), not real directories
- S3 behaves like cloud storage (similar UX to Google Drive/Dropbox)
