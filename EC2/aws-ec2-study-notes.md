# Amazon EC2 – Complete Study Notes (AWS CCP)

## Table of Contents

1. [What Are EC2 Instances?](#1-what-are-ec2-instances)
2. [EC2 Capabilities](#2-ec2-capabilities)
3. [User Data Script](#3-user-data-script)
4. [EC2 Sizing & Configuration](#4-ec2-sizing--configuration)
5. [EC2 Instance Types](#5-ec2-instance-types)
6. [AWS Right Sizing](#6-aws-right-sizing)
7. [EC2 Purchasing Options](#7-ec2-purchasing-options)
8. [Security Groups (Firewall)](#8-security-groups-firewall)
9. [EBS Volumes](#9-ebs-volumes)
10. [EBS Snapshots](#10-ebs-snapshots)
11. [Amazon Machine Image (AMI)](#11-amazon-machine-image-ami)
12. [EC2 Image Builder](#12-ec2-image-builder)
13. [EC2 Instance Store](#13-ec2-instance-store)
14. [EFS – Elastic File System](#14-efs--elastic-file-system)
15. [Storage Comparison: EBS vs EFS vs Instance Store](#15-storage-comparison-ebs-vs-efs-vs-instance-store)
16. [Amazon FSx](#16-amazon-fsx)
17. [Master Exam Cheat Sheet](#17-master-exam-cheat-sheet)

---

## 1. What Are EC2 Instances?

**Amazon EC2 (Elastic Compute Cloud)** = a virtual server (instance) that runs in the AWS Cloud. The instance type you choose determines the CPU, memory, storage, and network capacity available to it.

- You rent compute capacity **by the second (Linux) or by the hour**, instead of buying and maintaining physical servers.
- Each instance type offers a different balance of compute, memory, network, and storage, so choosing the right type is a **cost/performance decision**, not just a technical one.
- EC2 is the textbook example of **Infrastructure as a Service (IaaS)**.

| Who manages... | AWS | You |
|---|---|---|
| Physical hardware | ✅ | |
| Virtualization layer | ✅ | |
| Operating system | | ✅ |
| Runtime | | ✅ |
| Application | | ✅ |

> **Exam tip:** If a question describes *"full control over the OS and installed software, with AWS managing the underlying hardware,"* the answer is **EC2 / IaaS**, not a managed or serverless service like Lambda or RDS.

---

## 2. EC2 Capabilities

| Capability | Service |
|---|---|
| Renting virtual machines | EC2 |
| Storing data on virtual drives | EBS |
| Distributing load across machines | ELB |
| Scaling the services | Auto Scaling Group (ASG) |

Other capabilities:

- Wide range of instance types for different use cases: general-purpose, memory-intensive, GPU processing.
- Scale compute capacity **up or down** based on demand (cost optimization + performance scalability).
- Supports many operating systems and software configurations.
- Widely used for hosting websites, running applications, and data processing.

### When is EC2 the right (or wrong) answer?

| Scenario signal | Best answer |
|---|---|
| Mentions "provisioning," "patching the OS," or "choosing an instance type" | **EC2** |
| Needs **zero server management** | Lambda |
| Storing/retrieving files as **objects** (not a running OS) | S3 |

---

## 3. User Data Script

EC2 instances can be configured at launch using **User Data** scripts.

**Bootstrapping** = executing commands upon a machine's startup.

| Property | Detail |
|---|---|
| When it runs | Only **once**, on first boot |
| Re-runs on reboot? | No (unless explicitly configured) |
| Privileges | Runs as **root / administrator** |

### Common uses

- Installing updates and software packages
- Downloading files or application code from the internet
- Running configuration commands (e.g., starting a web server)
- Any other one-time setup task at launch

### Common exam trap: User Data vs IAM Role vs Instance Metadata

| Concept | Purpose |
|---|---|
| **User Data** | Runs commands at instance startup |
| **IAM Role** | Grants permissions to the instance |
| **Instance Metadata** | Info about the instance itself, retrieved via the metadata service |

> Example: a User Data script can create a server and a static web page when the EC2 instance is first created.

---

## 4. EC2 Sizing & Configuration

| Component | Options / Notes |
|---|---|
| **Operating System (OS)** | Linux, Windows, or macOS |
| **Compute Power & Cores (CPU)** | Determine CPU capacity and number of cores |
| **RAM** | Specify the memory needed |
| **Storage – Network-attached** | EBS (Elastic Block Store), EFS (Elastic File System) |
| **Storage – Hardware** | EC2 Instance Store |
| **Network Card** | Network speed + Public IP address |
| **Firewall Rules** | Security groups (inbound/outbound) |
| **Bootstrap Script** | EC2 User Data (runs at first launch) |

---

## 5. EC2 Instance Types

### Naming convention – example: `m6g.2xlarge`

| Part | Meaning |
|---|---|
| `m` | Instance **family** (general purpose, compute optimized, etc.) |
| `6` | **Generation** number (higher = newer; generally better price-performance) |
| `g` | Optional **attribute**: `g` = AWS Graviton (ARM), `n` = network optimized, `d` = local NVMe storage |
| `2xlarge` | **Size** within the family (nano → micro → … → xlarge → 2xlarge → 4xlarge …) |

> You won't be asked to decode obscure letter codes, but recognize the family letters (m, c, r, t, g, p, i, d, etc.).

### Main instance families

| # | Family | Letters | Best for |
|---|---|---|---|
| 1 | **General Purpose** | M, T | Web servers, code repositories; balanced compute/memory/network |
| 2 | **Compute Optimized** | C | Batch processing, media transcoding, high-performance web servers, HPC, scientific modeling & ML, dedicated gaming servers |
| 3 | **Memory Optimized** | R, X | High-performance relational/non-relational databases, distributed web-scale caches, in-memory BI databases, real-time processing of large unstructured data |
| 4 | **Accelerated Computing** | P, G, Inf | ML training & inference, graphics rendering, video processing, parallel scientific/financial modeling, HPC |
| 5 | **Storage Optimized** | I, D, H1 | High-frequency OLTP, relational & NoSQL databases, Redis-style caches, data warehousing, distributed file systems |

### Details per family

| Family | Key characteristics |
|---|---|
| General Purpose | Balances compute, memory, and networking |
| Compute Optimized | High-performance processors for compute-intensive tasks |
| Memory Optimized | Fast performance on large data sets **in memory** |
| Storage Optimized | High, sequential read/write access to large data sets on **local storage** |
| Accelerated Computing | Hardware accelerators (GPUs, FPGAs, or AWS chips like Inferentia/Trainium) outperform CPU-based software for graphics & ML |

> **Exam tip:** "GPU" or "machine learning workload" → **Accelerated Computing** (P, G, Inf/Trn), **not** Compute Optimized. The C family is about high vCPU performance, not specialized hardware acceleration.

---

## 6. AWS Right Sizing

- Selecting the most powerful instance isn't always best, thanks to cloud elasticity.
- **Right sizing** = matching instance types and sizes to workload requirements for optimal performance **and** cost.
- Scaling up is easy, so **start small**.
- Review deployed instances to find opportunities for downsizing without hurting capacity.
- Right size **before migration** and **continuously after onboarding**, since requirements change.

### Key tools

| Tool | Role |
|---|---|
| **AWS Compute Optimizer** | Main purpose-built tool. Uses ML on CloudWatch metrics to recommend optimal EC2, EBS, Lambda, and ASG configurations; flags resources as over-provisioned, under-provisioned, or optimized |
| **Amazon CloudWatch** | Provides raw utilization metrics (CPU, network, etc.) |
| **Cost Explorer** | Rightsizing recommendations focused on cost savings |
| **AWS Trusted Advisor** | Flags low-utilization EC2 instances (cost optimization checks) |

> **Exam tip:** "Automated recommendations to resize EC2 instances based on utilization" → **AWS Compute Optimizer**.

---

## 7. EC2 Purchasing Options

### 7.1 Overview comparison

| Option | Commitment | Max discount | Best for | Key caveat |
|---|---|---|---|---|
| **On-Demand** | None | – (baseline price) | Unpredictable/short-term workloads, dev/test | Highest per-hour cost for steady workloads |
| **Reserved Instances (RI)** | 1 or 3 years | Up to 72% | Steady-state workloads (e.g., 24/7 database) | Locked to specific attributes |
| **Savings Plans** | 1 or 3 years ($/hour) | Up to 72% | Consistent usage with flexibility | Commit to spend amount |
| **Spot Instances** | None | Up to 90% | Fault-tolerant, stateless, flexible-time jobs | Can be interrupted (2-min warning) |
| **Dedicated Hosts** | On-Demand or Reservation | Reservation up to 70% | BYOL per-socket/core licensing, compliance | Most expensive |
| **Dedicated Instances** | None | – | Physical isolation for compliance | No control over placement |
| **Capacity Reservations** | None (pay On-Demand rate) | – | Guaranteed capacity in a specific AZ | Pay even if unused |

### 7.2 On-Demand Instances

- Pay-as-you-go; **no long-term commitment**, no upfront payment.
- Start and stop instances as needed.

| Use case | Why |
|---|---|
| Variable workloads | Unpredictable or fluctuating usage |
| Development & testing | Temporary environments |
| Short-term projects | Quick provisioning |

### 7.3 Reserved Instances

- Reserved capacity for a specific instance configuration in a region (or AZ).
- Commit to specific attributes: **Instance Family/Type, Region, Tenancy, OS**.
- Terms: **1-year or 3-year**.
- "Up to 72%" is a **maximum**, not a fixed rate; actual discount depends on term, payment option, and class.
- Zonal RI (specific AZ) also acts as a **capacity reservation**.

**Payment options**

| Payment option | Discount |
|---|---|
| All Upfront | Largest |
| Partial Upfront | Medium |
| No Upfront | Smallest (still cheaper than On-Demand) |

**Offering classes**

| Class | Max discount | Flexibility |
|---|---|---|
| **Standard RI** | Up to 72% | Cannot change instance family/attributes |
| **Convertible RI** | Up to 66% | Can exchange for different instance family, OS, or tenancy |

> **Exam tip:** AWS now recommends **Savings Plans** over RIs for most scenarios. "Most flexible way to save on consistent compute usage" → **Savings Plans**.

### 7.4 Savings Plans

- Commit to a consistent amount of compute usage (**$/hour**) for **1 or 3 years**.
- Up to **72%** discount vs On-Demand.
- Applied as a **spend commitment**, not a specific instance reservation.

| Type | Flexibility | Discount | Applies to |
|---|---|---|---|
| **Compute Savings Plans** | Most flexible | Lower | Any EC2 family/size/OS/region, plus **Fargate** and **Lambda** |
| **EC2 Instance Savings Plans** | Locked to instance family in a region | Deeper | Can still change size, OS, or AZ within that family |

> **Key differentiator vs RIs:** flexibility. RIs lock you to an exact configuration; Savings Plans let usage shift across families, regions, or even services.

### 7.5 Spot Instances

- Use **spare/unused EC2 capacity** at a discounted, fluctuating price.
- **No more bidding:** you request Spot (optionally with a max price cap) and pay the current Spot price.
- Up to **90%** savings vs On-Demand.
- **Trade-off:** AWS can reclaim the instance with a **2-minute warning**.

| ✅ Good for | ❌ Never use for |
|---|---|
| Fault-tolerant, stateless apps | Critical databases |
| Batch jobs & big-data processing | Workloads with strict SLAs |
| CI/CD build fleets | Anything that can't handle sudden shutdown |
| Image/video rendering | |
| Analytics clusters, containerized workloads | |

### 7.6 Dedicated Hosts

- A **physical server fully dedicated** to you, with visibility into sockets and physical cores.
- **Most expensive** option.
- Required for **per-socket, per-core, or per-VM software licenses** (e.g., some Microsoft or Oracle licenses).
- Purchase as On-Demand or as a Reservation (up to **70%** off).

| Use case | Example |
|---|---|
| Sensitive/regulated data | Healthcare organization needing dedicated infrastructure |
| BYOL | Existing Windows Server, SQL Server, or Oracle licenses tied to sockets/cores |

### 7.7 Dedicated Instances

- Run on hardware dedicated to **your AWS account**; no other customer's instances share that server.
- Enhanced isolation, predictable performance, compliance support.
- May share hardware with other instances **in the same AWS account**.
- **No control over placement**; hardware may change after Stop/Start.
- Generally **cheaper than Dedicated Hosts**.
- Use case: a bank needing assurance no other tenant shares the physical hardware.

### 7.8 Dedicated Hosts vs Dedicated Instances

| Feature | Dedicated Host | Dedicated Instance |
|---|---|---|
| Physical server dedicated to you | ✅ | ✅ (account-level single tenancy) |
| Visibility into sockets/cores | ✅ | ❌ |
| Control over instance placement | ✅ | ❌ |
| Per-socket/per-core BYOL licensing | ✅ | ❌ |
| Cost | Higher | Lower |

### 7.9 Capacity Reservations

- Reserve capacity for specific instance types in a **specific AZ**.
- Guarantees capacity availability; launch instances into the reservation when needed.
- Can be combined with On-Demand, Reserved Instances, or Savings Plans pricing.
- Use case: mission-critical workloads needing capacity assurance.

### 7.10 How to choose a purchasing option

| Step | Action |
|---|---|
| 1. Define workload requirements | Compute, memory, storage, networking needs |
| 2. Analyze performance characteristics | CPU, memory size, network performance |
| 3. Consider cost optimization | On-Demand, RIs, Savings Plans, Spot |
| 4. Review use case suitability | General Purpose, Compute, Memory, Storage optimized, etc. |
| 5. Evaluate instance families | Test and benchmark before deployment |
| 6. Monitor and optimize | Track metrics and adjust continuously |

### 7.11 Scenario → Answer quick reference

| Scenario | Answer |
|---|---|
| Unpredictable, short-term workload, new project | **On-Demand** |
| Steady-state production DB running 24/7 for 3 years | **Reserved Instance or Savings Plan** |
| Flexible compute spend across EC2, Fargate, and Lambda | **Compute Savings Plan** |
| Interruption-tolerant batch/big-data job; cost is top priority | **Spot Instance** |
| Must use existing per-core software licenses | **Dedicated Host** |
| Guaranteed AZ capacity for a launch, without committing to pricing | **On-Demand Capacity Reservation** |

---

## 8. Security Groups (Firewall)

Security groups act as a **firewall** for EC2 instances.

### What they regulate

| Controls | Detail |
|---|---|
| Ports | Access to specific ports |
| IP ranges | Authorized ranges (IPv4 and IPv6) |
| Inbound traffic | From others to the instance |
| Outbound traffic | From the instance to others |

### Key properties

| Property | Detail |
|---|---|
| Attachment | Can be attached to **multiple instances** |
| Scope | Tied to a **region + VPC** combination |
| Location | Operates **outside** the instance; blocked traffic never reaches it |
| State | **Stateful**: allowed inbound → response automatically allowed out |
| Rule types | **Allow rules only** (no explicit deny) |
| Default inbound | **All blocked** |
| Default outbound | **All allowed** |

> Best practice: maintain a **separate security group for SSH/RDP** access.

### Troubleshooting

| Symptom | Likely cause |
|---|---|
| **Timeout** (app not accessible) | Security group misconfiguration (traffic silently dropped) |
| **Connection refused** | Security group is fine; the application isn't running or isn't listening on that port |

### Security Group vs NACL

| Feature | Security Group | Network ACL (NACL) |
|---|---|---|
| State | **Stateful** | **Stateless** |
| Rule types | Allow only | Allow **and** Deny |
| Response traffic | Automatically allowed | Must be allowed explicitly in both directions with separate rules |

---

## 9. EBS Volumes

An **EBS (Elastic Block Store) volume** is a **network drive** that can be attached to instances while they run. Think of it as a **"network USB stick."**

| Property | Detail |
|---|---|
| Persistence | Data persists independently of the EC2 instance lifecycle (as long as the volume isn't deleted) |
| Attachment (CCP level) | One instance at a time (io1/io2 Multi-Attach is an advanced exception) |
| Availability Zone | **Locked to one AZ** (e.g., `us-east-1a` volume can't attach to an instance in `us-east-1b`) |
| Network | Not physical; communicates via the network, so some **latency** is possible |
| Mobility | Can be quickly detached from one instance and attached to another |
| Capacity | Provisioned in **GB and IOPS**; can be **increased** over time |
| Billing | Based on **provisioned** capacity, regardless of usage |
| Default SSD type | **gp3** (recommended over gp2): IOPS/throughput decoupled from size |

- To move a volume across AZs: **create a snapshot first**.
- **Free Tier:** legacy accounts get 30 GB/month of EBS (General Purpose SSD or Magnetic) for the first 12 months. Accounts created after **July 15, 2025** get up to **$200 in Free Tier credits** applicable to eligible services, including EBS. Check current terms, as the model has changed.

### Delete on Termination attribute

Controls EBS behavior when the EC2 instance terminates.

| Volume | Default | Attribute |
|---|---|---|
| **Root** volume | **Deleted** | Enabled |
| **Other attached** volumes | **Not deleted** | Disabled |

- Configurable via AWS Console / CLI.
- Use case: **preserve the root volume** when the instance is terminated.

---

## 10. EBS Snapshots

- A **backup of an EBS volume at a point in time**.
- Detaching the volume isn't required, but is recommended for consistency.
- Snapshots can be **copied across AZs or Regions**.

| Feature | Description | Key facts |
|---|---|---|
| **EBS Snapshot Archive** | Move snapshots to a cheaper "archive tier" | Up to **75% lower** cost; restore takes **24–72 hours**; per-GB retrieval fee; **90-day minimum** retention |
| **Recycle Bin for EBS Snapshots** | Retain deleted snapshots to recover from accidental deletion | Retention rule: **1 day to 1 year** |

---

## 11. Amazon Machine Image (AMI)

**AMI** = a pre-configured template used to launch an EC2 instance. Think of it as a "snapshot" of a fully set-up server: OS, software, configuration, monitoring agents.

- **Faster boot/configuration**: software is pre-packaged, so no need to reinstall via User Data every launch.
- Built for (and tied to) a **specific region**, but can be **copied to other regions**.

### AMI sources

| Type | Description |
|---|---|
| **Public AMI** | Provided/maintained by AWS (e.g., Amazon Linux AMI) |
| **Your own AMI** | You build and maintain it, customized for your apps |
| **AWS Marketplace AMI** | Pre-built by a third party (potentially sold), e.g., with a commercial security tool pre-installed |

### How to build a custom AMI

| Step | Action |
|---|---|
| 1 | Launch and fully configure an EC2 instance (app, dependencies, config files) |
| 2 | **Stop** the instance to ensure data integrity |
| 3 | Create an AMI from the stopped instance (this automatically creates underlying EBS snapshots) |
| 4 | Launch new instances from the AMI whenever needed (e.g., in an Auto Scaling Group) |

---

## 12. EC2 Image Builder

| Property | Detail |
|---|---|
| Purpose | Automates creation of VM or container images |
| Automates | Creation, maintenance, validation, and testing of EC2 AMIs |
| Scheduling | Weekly, or whenever packages are updated |
| Cost | **Free service**; you pay only for underlying resources used |

---

## 13. EC2 Instance Store

- EBS is a network drive with network-limited performance.
- For **maximum I/O performance**, use **Instance Store**: physically attached **NVMe SSD** storage on the host, with much higher IOPS than EBS.

| Property | Detail |
|---|---|
| Persistence | **Ephemeral**: data lost if the instance is **stopped, hibernated, or terminated** (a simple **reboot is safe**) |
| Good for | Buffer, cache, scratch data, temporary content |
| Never for | Data you can't afford to lose |
| Risk | Data loss if underlying hardware fails |
| Backups/replication | **Your responsibility** |
| Practical examples | Redis cache node; Hadoop/Spark worker node (can recompute from source of truth) |

> **Exam tip:** "Ephemeral storage," "highest possible disk I/O," or "data loss on stop" → **Instance Store**. "Persists independently of the instance" → **EBS**.

---

## 14. EFS – Elastic File System

**EFS** = managed **NFS** (network file system) that can be mounted on **hundreds/thousands of EC2 instances concurrently**.

| Property | Detail |
|---|---|
| OS support | **Linux** EC2 instances (not natively Windows) |
| Multi-AZ | Can be mounted from instances across **multiple AZs** in the same region simultaneously (key difference from EBS) |
| Availability/durability | Highly available; data stored redundantly across multiple AZs |
| Scaling | Automatic, no capacity planning |
| Pricing | Pay for storage used; noticeably **more expensive per GB** than EBS gp3 |

> **Exam tip:** "Shared file storage accessible by many Linux EC2 instances at once across multiple AZs" → **EFS**. "Block storage attached to a single instance in one AZ" → **EBS**.

### EFS storage classes

| Class | Access pattern | Savings | Notes |
|---|---|---|---|
| **Standard** | Frequent | – | Default |
| **Infrequent Access (EFS-IA)** | A few times a quarter | Up to **94%** lower than Standard | Small per-GB fee on each read/write; best for genuinely cold files |
| **Archive** | A few times a year | Further savings beyond IA | Coldest, cheapest |

### Tiering features

| Feature | Description |
|---|---|
| **Lifecycle Policy** | Auto-moves files by last access time (e.g., 30 days → IA, 90 days → Archive) |
| **Intelligent-Tiering** | Monitors access patterns and moves files **back to Standard** if accessed again; useful for unpredictable access |
| **Transparency** | Applications use the same file system namespace and mount point regardless of storage class |

---

## 15. Storage Comparison: EBS vs EFS vs Instance Store

| Feature | **EBS** | **EFS** | **Instance Store** |
|---|---|---|---|
| Type | Block storage | File storage (NFS) | Local (host-attached) storage |
| Sharing | Usually one instance | Multiple instances | Tied to the host |
| Multi-AZ | No (locked to one AZ) | Yes | No |
| Data persistence | Yes | Yes | Temporary (ephemeral) |
| Typical use | OS and databases | Shared files | Cache and temporary data |
| Performance | Good (network-limited) | Scalable | Highest I/O (NVMe) |
| OS support | Linux/Windows | Linux | Depends on instance |

---

## 16. Amazon FSx

**FSx** launches and manages **third-party, purpose-built file systems** for workloads that EFS/EBS don't natively support.

| FSx type | Description | Use case |
|---|---|---|
| **FSx for Windows File Server** | Managed Windows-native file system; **SMB** protocol + **Active Directory** integration | Lift-and-shift Windows apps needing a native Windows file share |
| **FSx for Lustre** | High-performance file system; integrates with **S3**; POSIX-compliant | ML and HPC workloads |
| **FSx for NetApp ONTAP** | Managed NetApp file system | Migrating NetApp workloads without re-architecting |
| **FSx for OpenZFS** | Managed ZFS file system | Migrating ZFS-based workloads without re-architecting |

> **Exam tip:** FSx = "AWS-managed version of a specific, named third-party file system." EFS = "AWS's own native NFS file system."

---

## 17. Master Exam Cheat Sheet

### Keyword → Answer

| If you see... | Think... |
|---|---|
| Full OS control, AWS manages hardware | **EC2 (IaaS)** |
| Zero server management | **Lambda** |
| Store files as objects | **S3** |
| Run commands at first boot | **User Data** |
| Grant permissions to an instance | **IAM Role** |
| Info about the instance itself | **Instance Metadata** |
| GPU / machine learning | **Accelerated Computing (P, G, Inf/Trn)** |
| Large in-memory datasets / caches | **Memory Optimized (R, X)** |
| Batch, HPC, transcoding | **Compute Optimized (C)** |
| Web servers, balanced | **General Purpose (M, T)** |
| High local disk I/O, OLTP, data warehousing | **Storage Optimized (I, D, H1)** |
| Automated resize recommendations | **AWS Compute Optimizer** |
| Flexible commitment discount | **Savings Plans** |
| Cheapest, interruption-tolerant | **Spot** |
| Per-core licensing (BYOL) | **Dedicated Host** |
| Single-tenant hardware, no placement control | **Dedicated Instance** |
| Guaranteed capacity in an AZ | **Capacity Reservation** |
| Stateful firewall, allow-only | **Security Group** |
| Stateless firewall, allow + deny | **NACL** |
| Connection **timeout** | Security group issue |
| Connection **refused** | Application issue |
| Persistent network block storage (1 AZ) | **EBS** |
| Shared Linux file system across AZs | **EFS** |
| Ephemeral, highest IOPS | **Instance Store** |
| Windows file share (SMB) | **FSx for Windows File Server** |
| ML/HPC high-performance file system + S3 | **FSx for Lustre** |
| Pre-baked server template | **AMI** |
| Automate AMI creation/testing | **EC2 Image Builder** |

### Key numbers

| Item | Number |
|---|---|
| Reserved Instances max discount | Up to **72%** (Standard), **66%** (Convertible) |
| Savings Plans max discount | Up to **72%** |
| Spot max discount | Up to **90%** |
| Dedicated Host reservation | Up to **70%** |
| Spot interruption warning | **2 minutes** |
| RI / Savings Plan terms | **1 or 3 years** |
| EBS Snapshot Archive savings | Up to **75%** |
| Snapshot Archive restore time | **24–72 hours** |
| Snapshot Archive minimum retention | **90 days** |
| Recycle Bin retention | **1 day – 1 year** |
| EFS-IA savings | Up to **94%** |
