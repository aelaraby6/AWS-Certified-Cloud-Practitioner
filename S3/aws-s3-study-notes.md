# AWS S3 and Storage: Condensed Study Notes (Cloud Practitioner)

## 1. S3 Basics
- Object storage, effectively unlimited. Object size is 0 B to 5 TB; a single PUT is capped at 5 GB, so use **multipart upload** above that.
- Features: versioning, lifecycle policies, access controls, Transfer Acceleration, and integration with Athena, CloudFront, Lambda and Glue.
- Use cases: backup, archive, static websites, content distribution, data lakes.
- **Exam tip:** "Store files/objects" with no file system or database mentioned → **S3**.

## 2. Buckets and Objects
- Bucket names are **globally unique**, but buckets live in **one region**.
- Naming rules: 3-63 characters, lowercase letters, numbers and hyphens only, no underscores, not an IP address, must start with a letter or number, no `xn--` prefix, no `-s3alias` or `--ol-s3` suffix.
- Memory hook: `my-cool-bucket-2026` ✅, `My_Bucket` ❌

## 3. Access Control
| Control | Notes |
|---|---|
| **Bucket policy** | Resource-based JSON policy attached to the bucket. Sets who (Principal), what (Action) and which resources, with Allow or Deny. |
| **IAM policy** | Attached to a user or role. |
| **Block Public Access** | On by default. It **prevents** public access. |
| **IAM Access Analyzer for S3** | **Detective** control. It flags buckets exposed publicly or to outside accounts, and does not block anything. |

- Bucket policies are used for: public access (static websites), forcing encryption, cross-account access, and HTTPS-only (deny when `aws:SecureTransport` is false).
- S3 evaluates IAM policy, bucket policy and Block Public Access together.
- **Anti-pattern:** making a whole bucket public to share a few files. Keep it private and use **presigned URLs** (temporary access).

## 4. Durability vs Availability
- **Durability:** 11 9's (99.999999999%) for **all** classes, stored across at least 3 AZs.
- **Availability** varies by class:
  - Standard: 99.99%
  - Standard-IA and Glacier Instant Retrieval: 99.9%
  - One Zone-IA: 99.5%
  - Glacier Flexible Retrieval and Deep Archive: 99.99% per AWS docs (verify against current docs; the original notes grouped all Glacier at 99.9%)
- **One Zone** classes store data in a single AZ. Use them only for re-creatable data or secondary copies.

## 5. Storage Classes
| Class | Use when | Key points |
|---|---|---|
| **Standard** | Frequent access | Default, multi-AZ |
| **Express One Zone** | Latency-sensitive (ML, real-time analytics) | Single-digit ms, single AZ, directory bucket |
| **Standard-IA** | Needed instantly, accessed less than monthly | Multi-AZ, cheaper |
| **One Zone-IA** | Same, but re-creatable data | Single AZ, about 20% cheaper |
| **Glacier Instant Retrieval** | Archive accessed about quarterly | Millisecond retrieval |
| **Glacier Flexible Retrieval** | Archive accessed 1-2 times a year | Minutes to hours, free bulk retrieval |
| **Glacier Deep Archive** | Compliance retention, 7-10+ years | Cheapest, retrieval takes hours |
| **Intelligent-Tiering** | **Unknown or changing** access patterns | Auto-tiering, no retrieval fees |

- Lifecycle policies move objects between classes automatically. Classes can be mixed in one bucket.
- **Extra notes (not in the original):**
  - Intelligent-Tiering charges a small per-object monitoring fee, and objects under 128 KB are not auto-tiered.
  - Minimum storage durations: IA classes 30 days, Glacier Instant and Flexible Retrieval 90 days, Deep Archive 180 days. Early deletion is still billed.

## 6. Static Website Hosting
- S3 can serve HTML, CSS, JS and images with no web server.
- The website endpoint is **HTTP only**. Use **CloudFront** in front for HTTPS and a custom domain.
- It needs **hosting enabled plus a policy allowing public reads**. Enabling hosting alone does not make the bucket public.

## 7. Versioning
- Enabled **per bucket**. Best practice is to turn it on.
- Each overwrite creates a new version. Version IDs are random strings, not 1, 2, 3.
- Objects that existed before versioning was enabled get version ID `null`.
- **Delete** adds a **delete marker**. To remove permanently, delete the specific version ID.
- **Cannot be disabled, only suspended.** Suspending keeps existing versions.
- **MFA Delete** requires MFA to permanently delete a version or change versioning state.

## 8. Replication (CRR / SRR)
- Versioning is **required on both** buckets. Replication is asynchronous. The buckets can be in different accounts, and S3 needs IAM permissions.
- **Not retroactive:** only new objects are replicated. Use **S3 Batch Replication** for existing objects.
- Delete markers are not replicated by default. Version-ID deletes are **never** replicated.
- **CRR** (cross-region): low latency for global users, disaster recovery.
- **SRR** (same-region): log aggregation, same-region compliance.

## 9. Encryption
- All new objects are encrypted by default with **SSE-S3** (AES-256, free) since January 2023.
- **SSE-S3:** AWS manages the keys.
- **SSE-KMS:** keys managed in KMS, with **CloudTrail audit trail** and finer key control.
- **SSE-C:** you supply the key and AWS never stores it.
- Encryption in transit = enforce HTTPS (separate from encryption at rest).
- Not in the original notes: client-side encryption and DSSE-KMS (dual-layer). Both are rarely tested at this level.

## 10. Shared Responsibility Model (S3)
- **AWS (security OF the cloud):**
  - Durability, availability and scalability
  - Default SSE-S3 encryption
  - Physical isolation of customer data
  - Hardware, storage and network patching
  - Controlling employee access to customer data
- **You (security IN the cloud):**
  - Block Public Access, bucket policies and ACLs
  - Optional stronger encryption (SSE-KMS, client-side)
  - IAM users, roles and permissions
  - Versioning, logging (Server Access Logs, CloudTrail) and MFA Delete
  - Data classification and lifecycle policies
- A misconfigured public bucket is a **customer** responsibility.

## 11. AWS Snow Family
- Portable, secure devices for **edge computing** and **one-time bulk data migration** (TBs to PBs) where the network is too slow or costly.
- **Since 7 Nov 2025, Snowball Edge can only be ordered by existing Snow Family customers.** New customers should use **DataSync** or the **Data Transfer Terminal**.
- **Snowball Edge models:**
  - **Storage Optimized:** 210 TB NVMe, up to 1.5 GB/s, for bulk transfer.
  - **Compute Optimized:** 104 vCPUs, 416 GiB RAM, 28 TB NVMe, optional GPU, clustering up to 16 nodes, for edge compute and ML.
- All models can run EC2 instances and Lambda (via IoT Greengrass), and have 1- and 3-year discounted options.
- **Usage process:**
  1. Order the device in the console.
  2. Install the Snowball client or **OpsHub**.
  3. Connect the device and copy data.
  4. Ship it back.
  5. AWS loads the data into S3.
  6. The device is wiped.
- **Edge computing use cases:** preprocessing data, running ML at the edge, transcoding media. Sites include trucks, ships and mines.
- **AWS OpsHub:** GUI tool (replaces most CLI use) to unlock and configure devices, transfer files, launch instances and monitor metrics.
- **Pricing shape:** flat per-job fee, plus extra daily charges, plus data transfer out. Data transfer **into S3 is free**.

## 12. AWS Data Transfer Terminal
- A **physical AWS facility** where you bring your own storage devices and upload directly over a high-throughput link (at least two 100 Gbps fiber links per site).
- Upload targets include S3 and EFS. Nothing is shipped.
- Currently offered to **Enterprise Support** customers at sites in cities such as Los Angeles, New York, London, Paris, Tokyo and Sydney.
- **Comparison:**
  - vs **Snowball Edge:** no shipping wait.
  - vs **Direct Connect:** Direct Connect is a persistent link; the Terminal is a one-time visit.
  - vs **DataSync / internet upload:** the Terminal is for when your bandwidth is too slow.
- Use cases: large video or audio files, sensor and vehicle test data.

## 13. AWS Storage Gateway
- **Hybrid storage** connecting on-premises systems to S3 with **ongoing, continuous** access. Snow Family is a **one-time** transfer.
- Use cases: disaster recovery, backup and restore, tiered storage.
- **Types:**
  - **File Gateway:** file data
  - **Volume Gateway:** block storage
  - **Tape Gateway:** virtual tape library

## Quick Exam Cheat Sheet
| Scenario keyword | Answer |
|---|---|
| Store files/objects | S3 |
| Unknown or changing access | Intelligent-Tiering |
| Accessed yearly, compliance | Glacier Deep Archive |
| Audit trail of key use | SSE-KMS |
| Share a few files temporarily | Presigned URL |
| Prevent public access | Block Public Access |
| Detect public or external access | IAM Access Analyzer for S3 |
| HTTPS static site | CloudFront in front of S3 |
| "Disable" versioning | Not possible, only suspend |
| Bulk offline migration | Snowball Edge (existing customers), Data Transfer Terminal |
| Recurring hybrid access | Storage Gateway |
| Snow Family GUI | OpsHub |
