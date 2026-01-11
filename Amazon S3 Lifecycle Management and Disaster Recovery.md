# Amazon S3 Lifecycle Management and Disaster Recovery Hands-on

## Objective

In this hands-on exercise, I configured **Amazon S3 bucket lifecycle management and disaster recovery features** to understand how S3 helps with **cost optimization**, **storage management**, and **compliance requirements**.
These features are essential for me as a **DevOps engineer**, especially because S3 is widely used across real-world projects.

---

## Part 1: S3 Lifecycle Rules – Cost Optimization

### Why I Did This

By default, S3 stores objects in the **Standard storage class**, which is meant for frequent access. However, if objects are not accessed often, continuing to store them in Standard results in unnecessary cost.
Lifecycle rules help automatically move objects to **cheaper storage classes** based on their age.

---

### Step 1: Accessing Lifecycle Rules

* I opened my **S3 bucket**
* Navigated to the **Management** tab
* Selected **Lifecycle rules**
* Clicked **Create lifecycle rule**

---

### Step 2: Creating the Lifecycle Rule

* I named the rule **winter-gallery-lifecycle-Transition**
* I applied the rule to **all objects** in the bucket

  * I noted that I could limit this using a prefix, but for this exercise, I applied it to all objects
  * The choice always depends on the use case

---

### Step 3: Considering Versioning

This bucket is **versioned**, so I made sure to define rules for:

* **Current object versions**
* **Non-current (older) versions**

If I only transitioned current versions, the older versions would still remain and continue consuming storage, which defeats the purpose of cost savings.

---

### Step 4: Storage Class Transitions for Current Versions

I configured transitions based on object age with the following settings:

* Standard → **Standard Infrequent Access** after **30 days**
* Standard Infrequent Access → **One Zone Infrequent Access** after **60 days**
* One Zone Infrequent Access → **Glacier Flexible Retrieval** after **90 days**
* Glacier Flexible Retrieval → **Glacier Deep Archive** after **180 days**

These values were examples, and I understand that real projects require choosing numbers based on actual access patterns.

---

### Step 5: Storage Class Transitions for Non-current Versions

I applied similar transitions to non-current versions:

* Move to Standard Infrequent Access after **35 days**
* Move to One Zone Infrequent Access after **65 days**
* Move to Glacier after **95 days**
* Move to Glacier Deep Archive after **185 days**

I ensured there was a **90-day difference** between Glacier and Glacier Deep Archive, which is required.

---

### Step 6: Expiring Objects

* I set objects to **expire after 450 days**

  * Since the bucket is versioned, this places a **delete marker**
* I then configured **permanent deletion of non-current versions** after **455 days**

This ensures that all data is fully removed after the defined lifecycle period.

---

### Step 7: Handling Incomplete Uploads

* I enabled deletion of **incomplete multipart uploads after 15 days**
* This prevents unused and partial uploads from wasting storage

---

### Step 8: Creating the Rule

* I reviewed all configurations
* Clicked **Create rule**
* The lifecycle rule was created successfully
* I confirmed that lifecycle rules can be edited, viewed, or deleted later

---

## Part 2: Disaster Recovery Using Cross-Region Replication

### Why I Did This

Even though S3 stores data across multiple availability zones, **disaster recovery and compliance** often require data to be stored in a **different region** or even a different part of the world.

---

### Step 1: Creating a Disaster Recovery Bucket

* I switched to a **different AWS region (Oregon)**
* Created a new bucket named **winter-gallery-disaster**
* The bucket was created successfully

---

### Step 2: Creating a Replication Rule

* I returned to my original bucket
* Opened **Management → Replication rules**
* Clicked **Create replication rule**

Configuration:

* Rule name: **winter-gallery-disaster-replication**
* Status: **Enabled**
* Scope: **Replicate all objects**
* Destination:

  * Same AWS account
  * Selected the bucket in the different region
  * Refreshed the page when needed

---

### Step 3: Versioning and Permissions

* I ensured **versioning was enabled** on the destination bucket
* I created a **new IAM role**

  * This allows the source bucket to replicate data to the destination bucket

---

### Step 4: Replication Settings

* Encryption: kept default encryption
* Destination storage class:

  * Chose **Infrequent Access**
  * Disaster recovery data is rarely accessed
* Replication time control and metrics:

  * Left disabled to avoid additional cost
* Delete marker replication:

  * Left as default

---

### Step 5: Existing Objects Decision

* I chose **not to replicate existing objects**
* Only **new objects uploaded after this rule** will be replicated
* This avoids unnecessary replication charges

---

## Observations

* Lifecycle rules are crucial for **long-term cost control**
* Replication is essential for **disaster recovery and compliance**
* Similar options exist in most object storage services

---

## Cleanup Process

To avoid unnecessary charges, I cleaned up all resources:

1. Deleted lifecycle rules
2. Deleted replication rules
3. Emptied all buckets
4. Permanently deleted each bucket by confirming the bucket name

---

## Outcome

By completing this exercise, I gained **practical, hands-on experience** with **Amazon S3 lifecycle management and cross-region replication**
