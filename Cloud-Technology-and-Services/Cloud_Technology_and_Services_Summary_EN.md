# Cloud Technology and Services — Exam Summary

---

## 1. AWS Organizations

**Definition:** A service to manage multiple AWS accounts under one organization (billing, governance, centralized management).

- **Management Account (Master):** creates and manages all Member Accounts.

### Cost Benefits
- Consolidated Billing → single invoice
- Volume discounts (EC2, S3, etc.)
- Shared RI / Savings Plans discounts across accounts
- Automated account creation via API / Control Tower
- Service Control Policies (SCPs) for org-wide permission control

### Multi-Account Strategy
- Separate account per department/environment (dev/test/prod) → stronger blast-radius isolation than VPCs alone
- SCPs = compliance guardrails | VPCs = network-level isolation within an account
- Service Quotas are tracked per account, per Region → splitting workloads across accounts = more quota headroom
- Dedicated Log Archive account (write-once, no delete) to protect CloudTrail/CloudWatch logs
- Standardized tagging across accounts for cost tracking

> 🧠 **Exam Tip:** Isolating environments / containing a security incident / separating billing by department → **multiple AWS accounts under an Organization**, not IAM or VPCs alone.

### Organizational Units (OUs)
Group related accounts to apply uniform policies and simplify management.

### Service Control Policies (SCPs)
- Org-wide guardrails — define the **maximum available permissions**; they do not grant access themselves
- Applied at: Root / OU / Account level
- **Never applied to the Management Account** — only restrict member accounts
- Affect all IAM users/roles in a member account, including root — **but not** service-linked roles
- An action works only if there's: **IAM Allow + no SCP Deny**

> 🧠 **Exam Tip:** Restricting the root user or blocking a whole account from a service/Region → **SCP**, not an IAM Policy (IAM only works at the single-account level).

### Resource Control Policies (RCPs)
- Newer sibling to SCPs — restrict **what can be done to a resource** (vs. SCPs, which restrict what a principal can do)
- Covers: S3, STS, KMS, Secrets Manager, SQS

### Consolidated Billing
- Single invoice for the whole organization
- Management account can disable RI discount sharing for any account (including itself)

---

## 2. AWS Global Infrastructure

**Used for:**
- Decreased latency (deploy closer to users)
- Disaster Recovery (fail-over between Regions)
- Attack protection (a distributed infrastructure is harder to attack)

> 🧠 **Exam Tip:** 39+ Regions, 123+ AZs, 750+ Edge Locations. Relationship: **Regions > AZs > Edge Locations**. Every Region has a minimum of 3 AZs.

### Regions
- Geographically independent locations, fully isolated from each other (this gives AWS global fault tolerance)
- Every Region ≥ 3 AZs

**Criteria for choosing a Region:**
1. Compliance (data residency laws)
2. Proximity (closer to users = lower latency)
3. Feature availability (not every service is available everywhere)
4. Pricing (varies by Region)

### Availability Zones (AZs)
- Isolated locations within a Region, each with its own power, cooling, and networking
- Connected via high-bandwidth, low-latency private links — fast enough for synchronous replication
- Deploying across multiple AZs = High Availability | Single AZ = single point of failure

### Edge Locations (Points of Presence)
- Sites separate from Regions and AZs, mainly used for caching content via CloudFront
- Far more numerous than Regions
- **Trap:** Do NOT run EC2 instances or databases — only cache and deliver content

---

## 3. AWS Route 53

A highly available, scalable DNS web service that connects user requests to applications.

### Routing Policies

| Policy | Description |
|---|---|
| Simple | Routes to a single resource, no health checks |
| Weighted | Distributes traffic by assigned weight values |
| Latency-Based | Routes to the Region with lowest latency |
| Failover | Routes to a standby resource on failure (requires a health check) |
| Geolocation | Routes based on user's geographic location (country/continent) — needs a default record or queries return no answer |
| Geoproximity | Routes based on user & resource location + a bias value (requires Traffic Flow) |
| Multi-Value Answer | Returns up to 8 healthy records at random — not a load balancer replacement |
| IP-Based | Routes based on the user's IP range |

> 🧠 **Exam Tip:** Geolocation = routes strictly **by user location** (e.g., "all EU traffic must go to Frankfurt"). Latency-Based = routes to whichever Region gives the **best performance**, regardless of location. Multi-Value Answer is DNS-level distribution with health checks, capped at 8 records — not a load balancer substitute.

---

## 4. AWS CloudFront

A CDN service for high performance, security, and developer convenience.

- S3 Bucket as origin: file distribution and edge caching — uses CloudFront Origin Access Control (OAC), replacing the older OAI
- CloudFront as ingress: allows direct uploads to an S3 bucket
- Custom Origins: ALB, EC2 Instance, S3 Website (must enable static hosting), any HTTP backend

### CloudFront vs S3 Cross-Region Replication

| CloudFront | S3 CRR |
|---|---|
| Global Edge network | Requires setup per Region |
| Cached for a TTL | Near real-time updates |
| Ideal for static content, global availability | Read-only, suitable for dynamic content needing low-latency access in specific regions |

---

## 5. S3 Transfer Acceleration

Speeds up transfers to/from S3 by 50–500% for long-distance transfers of large objects.

- Upload traffic enters the nearest CloudFront Edge Location first, then travels the rest of the way over AWS's private backbone
- You only pay the fee if acceleration actually helps — otherwise it falls back to a normal transfer at no extra charge
- Ideal use cases: mobile/web uploads & downloads from a globally distributed user base, transferring large media files between distant offices, data exchange with partners far from your bucket's Region

> 🧠 **Trap:** Don't confuse with CloudFront. Transfer Acceleration speeds up uploads/downloads **to your S3 bucket itself**; CloudFront caches and serves **already-stored content** to viewers.

---

## 6. AWS Global Accelerator

- Improves application availability/performance for global users using AWS's global network instead of the public internet
- Up to 60% performance improvement (TCP connections terminate at the edge)
- Provides 2 static Anycast IP addresses as a fixed entry point — no need to update DNS/allowlists when endpoints change
- Traffic enters the nearest Edge Location, then travels over AWS's private network to the optimal healthy endpoint (ALB/NLB/EC2/EIP)
- Supports TCP and UDP → good fit for gaming, IoT, VoIP (which CloudFront can't serve since it's HTTP/HTTPS only)

### CloudFront vs Global Accelerator

| CloudFront | Global Accelerator |
|---|---|
| CDN — caches content (images, video) | Doesn't cache — proxies packets at the edge |
| HTTP/HTTPS only | TCP/UDP |

> 🧠 **Quick decision rule:** Caching cacheable web content (images, video, HTML) → **CloudFront**. Improving performance/availability for non-HTTP protocols (gaming, VoIP, IoT) or needing static IPs → **Global Accelerator**.

---

## 7. AWS Outposts

"Server racks" bringing the same AWS infrastructure, services, APIs & tools on-premises as in the cloud.

- Consistent hybrid cloud experience across on-prem and cloud
- Seamless local data processing
- Fully managed, capacity-based pricing
- Great for low-latency apps needing fast access to on-prem data, and data residency compliance

**Benefits:** low-latency access to on-prem systems, local processing, data residency, easier migration path to the cloud, fully managed service

---

## 8. AWS Wavelength

Brings AWS infrastructure to the edge of 5G networks for ultra-low latency applications.

- Standard AWS compute/storage embedded directly inside telecom providers' data centers, at the edge of their 5G network
- Edge computing use cases + data residency compliance
- High-bandwidth, secure connection to the parent AWS Region via the carrier's network
- Wavelength Zones have their own pricing, separate from the parent Region; availability depends on telecom carrier partnerships

---

## 9. AWS Local Zones

Bring AWS services closer to large population, industry, and IT centers.

- Runs latency-sensitive applications
- Enables single-digit millisecond latency to end-users, or local data processing

---

## 10. AWS Global Applications Architecture

| Model | High Availability | Global Latency | Implementation Difficulty |
|---|---|---|---|
| Single Region, Single AZ | ❌ | ❌ | 🟢 Simple |
| Single Region, Multi AZ | 🟢 | ❌ | 🟠 Moderate |
| Multi Region, Active-Passive | 🟢 (Reads) | ❌ (Writes) | 🟠 Moderate |
| Multi Region, Active-Active | 🟢 (Reads & Writes) | 🟢 | 🔴 Very complex |

> 🧠 **Exam Tip:** Moving from Single Region/Single AZ toward Multi-Region Active-Active increases resilience and lowers global latency, but also increases operational overhead — especially around keeping write data consistent across Regions. Active-Active gives the best availability but is also the hardest and most expensive to implement correctly.

---

## 11. Docker & Container Services

### Docker
- A platform to package apps into containers that run on any OS
- Consistent behavior everywhere, no compatibility issues, works with any language/tech, scales in seconds

### Docker Images & Repositories
- **Public:** Docker Hub
- **Private:** Amazon ECR

### Amazon ECS (Elastic Container Service)
- Launches Docker containers on AWS
- **You** are responsible for provisioning/maintaining the infrastructure (EC2 instances)
- AWS handles starting/stopping the containers
- Integrates with Application Load Balancer for traffic distribution

### AWS Fargate
- Launch Docker containers **without** managing EC2 instances (serverless)
- AWS runs containers automatically based on specified CPU/RAM requirements

### Amazon ECR (Elastic Container Registry)
- Private, secure Docker registry — used by ECS/Fargate to pull images

---

## 12. Serverless & Lambda

### Serverless
- Developers focus on code, not server management (servers still exist, but are invisible/managed by the provider)
- Originally synonymous with FaaS; now includes managed databases, messaging, storage, etc.

### AWS Lambda
- No server provisioning/management
- Triggers functions in response to events from AWS services
- Time-limited execution (short runs only)
- Auto-scales based on demand
- Charges only for compute time used — nothing while idle

### Amazon API Gateway
- Fully managed API management
- Serverless and scalable
- Supports REST and WebSocket APIs
- Security, authentication, throttling, API keys, monitoring

### AWS Batch
- Fully managed batch processing at any scale
- Batch jobs have a defined start/end (unlike continuous jobs)
- Dynamically launches EC2 or Spot Instances as needed
- Jobs are defined as Docker images and run on ECS

**Batch vs. Lambda:**

| Batch | Lambda |
|---|---|
| No execution time limit | Time-limited execution |
| Any runtime packaged as a Docker image | Specific runtimes only |
| Uses EBS or instance store | Limited temporary disk space |
| Relies on EC2 | Fully serverless |

### AWS Lightsail
- Virtual servers, storage, databases, networking — with low, predictable pricing
- Simpler alternative to EC2/RDS/ELB/EBS/Route53
- Ideal for beginners, simple web apps (LAMP, WordPress, Node.js), dev/test environments
- High availability, but **no** auto-scaling and limited AWS integrations

---

## 13. Application Integration (Decoupling)

**Two communication patterns:**
1. Synchronous (application to application) — problematic during traffic spikes
2. Asynchronous / Event-based (application → queue → application)

### Amazon SQS (Simple Queue Service)
- Fully managed, serverless queue service to decouple applications
- Scales from 1 msg/sec to 10,000s/sec
- Default retention 4 days (max 14 days)
- No limit on number of messages in a queue
- Messages deleted after successful processing
- Low latency (< 10 ms on publish/receive)
- **FIFO** = First In First Out (message ordering preserved)

### Amazon Kinesis
Managed real-time big data streaming:
- **Data Streams:** low-latency ingestion from hundreds of thousands of sources
- **Data Firehose:** loads streams into S3, Redshift, ElasticSearch, etc.
- **Data Analytics:** real-time analytics using SQL
- **Video Streams:** monitors real-time video for analytics/ML

### Amazon SNS
- Pub/Sub model — one message to many subscribers via a Topic
- Every subscriber receives all messages sent to the topic
- Supports up to 12,500,000 subscriptions per topic; limit of 100,000 topics

### Amazon MQ
- Managed message broker for migrating on-prem apps using open protocols (MQTT, AMQP, STOMP, Openwire, WSS)
- Scales less than SQS/SNS, runs on servers, supports Multi-AZ failover
- Offers both queue features (like SQS) and topic features (like SNS)
- Supports: RabbitMQ, ActiveMQ

---

## 14. Infrastructure as Code (IaC)

### AWS CloudFormation
- Model, provision, and manage AWS and third-party resources as code (IaC)
- **Benefits:**
  1. **IaC:** no manual resource creation, changes reviewed via code
  2. **Reuse:** use templates already available online, plus existing docs
  3. **Productivity:** easily destroy/recreate infrastructure, auto-generated diagrams, declarative (no need to specify ordering)
  4. **Cost:** every resource in a stack gets tagged for cost tracking; can estimate costs from the template
  5. Supports almost all AWS resources (custom resources for anything unsupported)

### AWS CDK (Cloud Development Kit)
- Model applications using common programming languages
- Code compiles into a CloudFormation template
- Can deploy infrastructure and application runtime code together (Lambda, ECS/EKS containers)

---

## 15. Elastic Beanstalk

Solves problems around: setting up databases/load balancers, scalability, and letting devs focus on code.

**3 architecture models:**
- **Single Instance:** good for development
- **LB + ASG:** great for production/pre-production web apps
- **ASG only:** great for non-web apps in production

### Elastic Beanstalk
- Managed service: sets up the instance/OS and handles how the app is deployed
- Deployment strategy is configurable (capacity provisioning, load balancing, auto scaling, health monitoring)
- Developer is only responsible for application code
- **Health Monitoring:** a health agent pushes metrics to CloudWatch, checks app health, and publishes health events

---

## 16. CI/CD & Developer Tools

### AWS CodeDeploy
- Fully managed deployment service automating software deployments to various compute services
- Hybrid service — servers/instances must be pre-provisioned with the CodeDeploy Agent
- Use cases: automate deployments, deploy to many hosts, advanced deployment techniques, health monitoring + rollback

### AWS CodeCommit
- Secure, scalable, fully managed private Git repository hosting
- Real-time collaboration, immediate versioning of code changes
- Up to 5,000 repositories by default

### AWS CodeBuild
- Fully managed CI service: compiles source code, runs tests, produces ready-to-deploy packages
- Fully managed, serverless, continuously scalable, secure

### AWS CodePipeline
- CI/CD service automating the steps to release software changes
- Pipeline model: stages (build, test, deploy) each requiring completed actions before moving on
- Fast delivery and rapid updates

### AWS CodeArtifact
- Managed artifact repository for securely storing/sharing software packages (dependencies)
- Developers and CodeBuild retrieve dependencies directly from it

### AWS Cloud9
- Cloud IDE to write, debug, and deploy code directly in the browser
- Enables cloud collaboration, usable anywhere with internet access

---

## 17. AWS Systems Manager (SSM)

- Manages EC2 and on-premises systems at scale (hybrid service)
- Operational insights into your infrastructure
- Automated patching for compliance
- Run commands across an entire fleet of servers
- **Parameter Store:** secure storage for configuration/secrets (API keys, passwords, configs)
  - Serverless, scalable, long-lasting, simple SDK
  - Access managed via IAM
  - Optional versioning and encryption

---

## 🎯 Key Exam Traps — Quick Recap

1. **Edge Locations** ≠ running EC2/DB — caching only
2. **SCP** ≠ IAM Policy — org-wide, never applied to the Management Account
3. **Geolocation** (routes by user location) ≠ **Latency-Based** (routes by best performance)
4. **Multi-Value Answer** ≠ Load Balancer (capped at 8 records)
5. **CloudFront** (caches HTTP only) ≠ **Global Accelerator** (TCP/UDP, no caching)
6. **Transfer Acceleration** (speeds up transfer to/from your own bucket) ≠ **CloudFront** (caches/serves already-stored content)
7. **ECS** (you manage EC2) ≠ **Fargate** (fully serverless)
8. **Batch** (no time limit) ≠ **Lambda** (time-limited)
9. **Active-Active** = best availability, but hardest and most expensive to implement
