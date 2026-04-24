## S3 Lifecycle Rule Configuration (Hands-On Overview)

- Lifecycle rules automate object management in S3 (transitions + deletions).

### Creating a Lifecycle Rule

- Go to **Management → Create lifecycle rule**
- Example name: `demo-rule`
- Scope: apply to **all objects in the bucket** (or use prefixes/tags)

---

## Available Lifecycle Actions

There are **5 main types of actions**:

1. **Transition current versions**
2. **Transition non-current versions**
3. **Expire current versions**
4. **Permanently delete non-current versions**
5. **Delete expired objects / delete markers / incomplete multipart uploads**

---

## 1. Transition Current Versions

- Applies to the **latest (active) version** of objects

Example lifecycle flow:
- After 30 days → move to **Standard-IA**
- After 60 days → move to **Intelligent-Tiering**
- After 90 days → move to **Glacier Instant Retrieval**
- After 180 days → move to **Glacier Flexible Retrieval**
- After 365 days → move to **Glacier Deep Archive**

- Multiple transitions can be defined over time

---

## 2. Transition Non-Current Versions

- Applies to **older versions** of objects (after being overwritten)

Example:
- After 90 days → move to **Glacier Flexible Retrieval**

- Useful for archiving old versions quickly

---

## 3. Expire Current Versions

- Automatically **delete current objects** after a set time

Example:
- Delete objects after **700 days**

---

## 4. Permanently Delete Non-Current Versions

- Removes old versions permanently

Example:
- Delete non-current versions after **700 days**

---

## 5. Cleanup Actions

- Delete:
  - Expired objects
  - Delete markers
  - Incomplete multipart uploads

- Helps reduce storage waste

---

## Lifecycle Timeline View

- S3 provides a **visual timeline** of:
  - Transitions
  - Expiration actions
- Helps verify lifecycle logic before applying

---

## Summary

- Lifecycle rules:
  - Automate cost optimization
  - Manage object lifecycle efficiently
- Combine:
  - Transitions (move to cheaper storage)
  - Expiration (delete unused data)
- Works in the background once configured

## S3 Event Notifications (Hands-On)

### Step 1: Create an S3 Bucket
- Create a new bucket (e.g., `stephane-v3-events-notifications`)
- Navigate to the bucket → **Properties**
- Scroll to **Event notifications**

---

## Event Notification Options

1. **Create Event Notification**
2. **Enable Amazon EventBridge integration**
   - Sends all S3 events to EventBridge for advanced processing

---

## Step 2: Create an Event Notification

- Name: `DemoEventNotification`
- Optional filters:
  - Prefix (e.g., folder)
  - Suffix (e.g., `.jpg`)
- Event type:
  - Select **Object Created (All)**

---

## Step 3: Choose Destination

Supported targets:
- SQS queue
- SNS topic
- Lambda function

Example: choose **SQS queue**

---

## Step 4: Create SQS Queue

- Go to SQS
- Create queue (e.g., `DemoS3Notification`)

---

## Step 5: Configure Permissions (Critical Step)

- By default, S3 cannot send messages to SQS
- Must update **SQS access policy**

### Steps:
1. Go to SQS queue → **Edit policy**
2. Use policy generator:
   - Type: SQS Queue Policy
   - Effect: Allow
   - Action: `SendMessage`
   - Resource: Queue ARN
3. Apply policy

- This allows S3 to send messages to the queue

---

## Step 6: Link S3 to SQS

- Return to S3 event notification setup
- Select the SQS queue
- Save configuration

- A **test message** is automatically sent to SQS

---

## Step 7: Verify Event Notification

1. Go to SQS → **Send and receive messages**
2. Click **Poll for messages**
3. Observe test message from S3

---

## Step 8: Trigger Event

- Upload a file (e.g., `coffee.jpg`) to S3
- Go back to SQS → poll for messages

### Result:
- New message appears in queue
- Message contains:
  - `eventName`: e.g., `ObjectCreated:Put`
  - Object key (file name)

---

## Example Use Case

- Upload image → S3 event → SQS message → processing system
- Example workflow:
  - S3 upload triggers event
  - SQS receives message
  - Worker processes image (e.g., generate thumbnail)

---

## Key Takeaways

- S3 Event Notifications can send events to:
  - SQS
  - SNS
  - Lambda
  - EventBridge (optional advanced integration)
- **Permissions must be configured properly** using resource-based policies
- Events enable **event-driven automation** in AWS
