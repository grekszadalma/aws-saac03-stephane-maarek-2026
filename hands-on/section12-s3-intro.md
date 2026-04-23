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

## Making an S3 Object Public Using a Bucket Policy

By default, Amazon S3 buckets and objects are private. To make objects publicly accessible via their public URL, you must modify both bucket settings and apply a bucket policy.

## Step 1: Disable Block Public Access

- Go to the **Permissions** tab of the bucket
- Locate **Block Public Access (bucket settings)**
- Edit settings and disable the block
- Confirm the warning

⚠️ Important:
- This is a sensitive action
- Only disable if you explicitly want public access
- Misconfiguration can lead to data leaks

## Step 2: Create a Bucket Policy

- Scroll to **Bucket Policy**
- You can:
  - Use AWS documentation examples
  - Or use the **AWS Policy Generator** (recommended for beginners)

### Using the Policy Generator

1. Select:
   - Policy Type: S3 Bucket Policy
   - Effect: Allow
   - Principal: `*` (everyone)
   - Action: `s3:GetObject`

2. Specify the resource:
   - Copy bucket ARN (e.g., `arn:aws:s3:::your-bucket-name`)
   - Append `/*` to target all objects:
     - `arn:aws:s3:::your-bucket-name/*`

3. Add statement and generate policy

## Step 3: Apply the Policy

- Copy the generated JSON policy
- Paste it into the bucket policy editor
- Save changes

### Resulting Behavior

- All users (public internet) can perform `GetObject`
- All objects in the bucket become publicly readable

## Step 4: Verify Public Access

- Go to an object (e.g., `coffee.jpg`)
- Copy the **Object URL** (public URL)
- Open it in a browser

### Expected Outcome

- Before policy: `AccessDenied`
- After policy: object is accessible publicly

## Key Concepts

- `Principal: "*"` → allows anyone
- `Action: s3:GetObject` → read access only
- `Resource: bucket-name/*` → applies to all objects

## Important Notes

- Public access requires BOTH:
  - Block Public Access disabled
  - Bucket policy allowing access
- This method makes the entire bucket public
- Use caution with sensitive data

## Summary

- S3 objects are private by default
- Public access requires explicit configuration
- Bucket policies are the main way to enable public access
- Always validate security implications before exposing data

## Amazon S3 Versioning – Hands-on Behavior

### Enabling Versioning
- Go to **S3 bucket → Properties → Bucket Versioning**
- Enable versioning at the bucket level
- After enabling:
  - Any overwritten file creates a **new version instead of replacing the old one**

---

### Updating a File with Versioning Enabled

Example workflow:
- Initial file: `index.html` → content: “I love coffee”
- Updated file: “I really love coffee”
- Uploading the same key (`index.html`) again:
  - Does NOT overwrite the original
  - Creates a **new version of the object**

Result:
- Visiting the website now shows the latest version:
  - “I REALLY love coffee”

---

### Viewing Versions in S3
- Enable **“Show versions”** in the S3 console
- You will see:
  - Multiple versions of the same file
  - Version IDs for each upload

Important observations:
- Files uploaded **before versioning was enabled**:
  - Have version ID = `null`
- Files uploaded **after enabling versioning**:
  - Have real version IDs

---

### Restoring a Previous Version (Rollback)
- With “Show versions” enabled:
  - Select an older version of the file
  - Delete the newer version (or unwanted version)
  - This is a **permanent delete of that specific version**
- Result:
  - S3 reverts to the previous version automatically
  - Website content rolls back (e.g., back to “I love coffee”)

---

### Delete vs Delete Marker

#### Normal Delete (without version selection)
- Adds a **delete marker**
- Does NOT remove the underlying object data
- Object appears deleted in normal view

#### What actually happens:
- Object is still stored in S3
- It is just hidden by the delete marker

---

### Restoring Deleted Objects
- Enable “Show versions”
- Find the **delete marker**
- Permanently delete the delete marker
- Result:
  - Previous object version becomes visible again
  - File is restored (e.g., coffee.jpg comes back)

---

### Key Takeaways
- Versioning prevents accidental data loss
- Deletes are reversible unless a specific version is permanently removed
- Delete markers allow soft deletion
- You can recover:
  - Overwritten files
  - Deleted files
  - Entire previous states of objects
<img width="1915" height="893" alt="image" src="https://github.com/user-attachments/assets/4bd71a1e-71cf-46c3-8614-4b484a562b21" />

## Amazon S3 Replication – Hands-On Notes

### Setup Overview
- Create two buckets:
  - **Origin bucket** (source)
  - **Replica bucket** (destination)
- Buckets can be:
  - Same region → **SRR (Same-Region Replication)**
  - Different regions → **CRR (Cross-Region Replication)**

### Required Configuration
- **Enable versioning on both buckets**
- Replication only works when versioning is enabled

---

### Creating a Replication Rule
- Go to **Origin bucket → Management → Replication rules**
- Create a new rule:
  - Name the rule
  - Enable it
  - Apply to all objects (or specific prefix if needed)
- Choose destination bucket:
  - Same account or different account
- Create or assign an **IAM role** (required for replication)
- Save rule

---

### Important Behavior
- Existing objects are **NOT replicated automatically**
  - Must use **S3 Batch Replication** if needed
- Only **newly uploaded objects** after enabling replication are copied

---

### Testing Replication
1. Upload a new object (e.g., `coffee.jpg`) to origin bucket
2. Wait a few seconds
3. Object appears in replica bucket

Key detail:
- **Version IDs are preserved** between source and destination

---

### Replicating Older Objects
- Re-uploading an existing file (e.g., `beach.jpg`) creates a new version
- This **new version gets replicated**

---

### Delete Marker Replication
- Can be enabled in replication settings
- When enabled:
  - Deleting an object in source bucket creates a **delete marker**
  - This delete marker is replicated to destination

Result:
- Object appears deleted in both buckets (without showing versions)

---

### Permanent Delete Behavior
- Deleting a **specific version ID** = permanent delete
- Permanent deletes:
  - **Are NOT replicated**
  - Destination bucket retains its copy

---

### Observing Deletes
- Without "Show versions":
  - Object appears deleted
- With "Show versions":
  - You can see:
    - Delete marker
    - Previous object versions still موجود

---

### Key Takeaways
- Replication is **asynchronous**
- Only **new versions** are replicated
- Version IDs are preserved across buckets
- Delete markers can be replicated (optional)
- Permanent deletes are NOT replicated (safety feature)
- Use Batch Replication for existing objects
