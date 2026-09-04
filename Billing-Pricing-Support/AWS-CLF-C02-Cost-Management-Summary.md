# AWS Certified Cloud Practitioner (CLF-C02) — Study Summary
### Topic: Account Management, Governance & Cost/Pricing

---

## 1. AWS Organizations

**Definition:** A management service to consolidate multiple AWS accounts into one organization for centralized management, billing, and governance.

- One **management account** ("master account") creates and manages all other member accounts.
- **Cost benefits:**
  - Consolidated billing → single payment method, one invoice
  - Volume discounts from pooled usage (EC2, S3, etc.)
  - Shared Reserved Instance / Savings Plans discounts across accounts
  - Automated account creation via API / AWS Control Tower
  - Org-wide access control via **Service Control Policies (SCPs)**

### Multi-Account Strategy
- Separate accounts per department/cost center/environment (dev/test/prod) → strongest blast-radius isolation (stronger than VPCs alone).
- Use SCPs for compliance guardrails; VPCs for network-level isolation *within* an account.
- Most service quotas are tracked **per account, per Region** → splitting workloads across accounts gives more quota headroom.
- Maintain a dedicated **Log Archive account** (write-once, no delete) for CloudTrail/CloudWatch logs — protects audit trail even if another account is compromised.
- Standardize tagging across accounts for consistent cost allocation.

> 🧠 **Exam tip:** Isolating environments / containing a security incident / separating billing by department → **multiple AWS accounts under one Organization**, not IAM policies or VPCs alone.

### Organizational Units (OUs)
- Group related accounts to apply uniform policies and simplify management.

### Service Control Policies (SCPs)
- Org-wide **guardrails** — define the *maximum* available permissions; do **not** grant access themselves.
- Applied at Organization root, OU, or account level.
- **Never applied to the management account** — only restrict member accounts.
- Affects **all IAM users/roles in a member account, including root** — but **not** service-linked roles.
- An action requires both an IAM/resource-based **Allow** AND **no SCP Deny**.

> 🧠 **Exam tip:** Restricting root user or blocking a whole account from a service/Region → **SCP** (org-wide), not IAM policy (single-account only).

**Resource Control Policies (RCPs)** — newer sibling to SCPs: restrict what can be done *to a resource* (vs. SCPs restrict what a *principal* can do). Covers S3, STS, KMS, Secrets Manager, SQS.

### Consolidated Billing
- Combines usage across accounts for volume pricing, RI, and Savings Plans discounts.
- Single invoice for the whole organization.
- Management account can disable RI discount sharing for any account (including itself).

---

## 2. AWS Control Tower

Automates setup of a secure, compliant **multi-account landing zone**, built on top of AWS Organizations.

- Auto-builds management account + pre-built OU structure (typically Security & Sandbox OUs) in minutes.
- Applies **guardrails** (built on SCPs + AWS Config rules) for ongoing enforcement.
- Detects **configuration drift** and flags it for remediation.
- Dashboard for monitoring compliance across all accounts.
- Best for: setting up a new multi-account environment, or standardizing an existing one.

> 🧠 **Exam tip:** **Organizations** = underlying account/billing management service. **Control Tower** = automation layer *on top of* Organizations that builds a best-practice landing zone. Control Tower requires Organizations; Organizations doesn't require Control Tower.

---

## 3. AWS Resource Access Manager (RAM)

- Share existing AWS resources across accounts/OUs/org **without duplicating** them.
- Supported: VPC subnets, Transit Gateways, Route 53 Resolver rules, License Manager configs, EC2 Dedicated Hosts, Aurora DB clusters, IPAM pools, and more.
- Share with specific account IDs, or OUs/whole org (if using Organizations).
- Access still governed by IAM policies + RAM permissions — sharing does **not** bypass access control.

> 🧠 **Exam tip:** RAM = "share existing infrastructure across accounts" (e.g., central networking account shares a Transit Gateway). Different from Organizations (account mgmt) and Service Catalog (deploying pre-approved *new* resources).

---

## 4. AWS Service Catalog

- Lets admins pre-approve a catalog of **CloudFormation-based products** (e.g., standard EC2 web server stack) for end users to self-service deploy.
- Ensures governance/compliance — users can only deploy approved, pre-configured products.

> 🧠 **Exam tip:** Developers need self-service deployment **without** broad IAM permissions or ability to customize architecture → **Service Catalog**.

---

## 5. Tracking Costs

### Billing Dashboard (Bills page)
- Monthly view of charges by service & linked account.
- Current period shows **estimated** charges based on metered usage; final invoice generated after month closes.

### AWS Free Tier Usage Dashboard
- Tracks usage against Free Tier limits in near real time.
- Note: Elastic Beanstalk, CloudFormation, Auto Scaling are free themselves, but resources they create (EC2/RDS/etc.) are billed normally.
- Can set a **Budgets** alert specifically for Free Tier usage.

### Cost Allocation Tags
- Tag resources → filter/group costs in Cost Explorer and CUR.
- **AWS-generated tags:** auto-applied, prefixed `aws:` (e.g., `aws:createdBy`).
- **User-defined tags:** custom key-value pairs, prefixed `user:` once activated.
- ⚠️ Tags must be **explicitly activated** in Billing console before appearing in cost reports.

### Tagging & Resource Groups
- Taggable: EC2, AMIs, load balancers, security groups, RDS, VPC resources, Route 53 zones, IAM users, etc.
- CloudFormation stacks can tag all created resources uniformly.
- Common keys: Name, Environment, Team/Owner, CostCenter, Project.
- **Resource Groups:** live collection of resources sharing a common tag.
- **Tag Editor:** bulk find/add/manage tags across resources & Regions.

### Cost and Usage Reports (CUR / CUR 2.0)
- Most detailed/comprehensive cost dataset — the **source of truth**.
- Line-item data: rates, pricing terms, RI/Savings Plans utilization.
- Hourly/daily/monthly granularity, delivered to an S3 bucket.
- Integrates with Athena, Redshift, QuickSight.

> 🧠 **Exam tip:** Most granular billing dataset for custom analysis / data warehouse → **CUR**, not Cost Explorer.

### Cost Explorer
- Visual, interactive tool — no setup or S3 bucket needed.
- Custom reports filtered/grouped by service, account, Region, tag, instance type, etc.
- Granularity: org-wide down to daily/hourly per resource.
- Savings Plans / RI recommendations based on historical usage.
- Forecasts costs up to 12 months ahead.
- **Free** to use for viewing/interactive analysis.

> 🧠 **Exam tip:** "Visualize spending trends" / "identify which service is driving cost increases" → **Cost Explorer**.

---

## 6. Monitoring Costs

### Billing Alarms in CloudWatch
- Billing metric data published **only in us-east-1** — must switch Region to view/alarm, regardless of where resources run.
- Represents **total worldwide** charges (account or consolidated billing family), not per-service/Region.
- Reflects **estimated actual** charges so far, not a forecast.
- Simple threshold alarm only — lacks granularity/automation of AWS Budgets.

> 🧠 **Exam tip:** "Billing alerts only work from us-east-1" is a classic fact. For anything beyond a simple total-spend threshold → **AWS Budgets**.

### AWS Budgets
- Alerts when actual/forecasted cost or usage crosses a threshold.
- **4 types:** Cost, Usage, Reservation (RI) utilization/coverage, Savings Plans utilization/coverage.
- RI budgets support EC2, ElastiCache, RDS, Redshift.
- Up to 5 SNS/email notifications per budget.
- Filterable by same dimensions as Cost Explorer (Service, Account, Tag, Region, etc.).
- **Pricing:**
  - Monitoring-only budgets → **free**, unlimited.
  - Action-enabled budgets (auto-restrict permissions / stop resources): first 2 free, then **$0.10/day** each.

> 🧠 **Exam tip:** Monitoring/notifications = free. Daily fee applies only once automated actions are enabled beyond the first two.

### AWS Cost Anomaly Detection
- ML-based, continuous monitoring — **no additional charge**.
- Detects one-time spikes and gradual increases without a fixed dollar threshold.
- Scopes: whole account, services, member accounts, tags, cost categories.
- Provides root-cause analysis (service/account/Region).
- Alerts: individual or daily/weekly digest via email/SNS.

> 🧠 **Exam tip:** Keyword **"unexpected"/"unusual" spending without a predefined threshold** → Cost Anomaly Detection. Fixed dollar threshold → AWS Budgets.

### AWS Service Quotas
- Notifies when approaching a quota (limit) threshold — act before throttling.
- CloudWatch alarms creatable from Service Quotas console.
- Quota increases: self-service request or support case.
- Quotas tracked **per account, per Region**.

### Trusted Advisor
- Continuous automated best-practice assessment — no agent needed.
- 6 categories: cost optimization, performance, security, resilience, operational excellence, service limits.
- **Basic Support (free):** core checks — mainly service limits + a few security checks (MFA on root, open SG ports).
- **Business Support+ and above:** full check library (incl. cost optimization), weekly refresh, Support API access.
- Legacy "Developer"/"Business" tiers merging into Business Support+ (old names retire Jan 1, 2027).

> 🧠 **Exam tip:** "Resilience" = newer name for "Fault Tolerance" category. Only Business Support+ and above unlock cost optimization checks.

---

## 7. Pricing Models in AWS

### 4 Core Pricing Principles
1. **Pay-as-you-go** — pay only for what's consumed, no commitment.
2. **Save when you reserve** — commit to a term for a discount (EC2 RIs, DynamoDB Reserved Capacity, ElastiCache Reserved Nodes, RDS RIs, Redshift Reserved Nodes).
3. **Pay less by using more** — volume discounts (e.g., S3 tiers).
4. **Pay less as AWS grows** — AWS passes infrastructure savings to customers.

> 🧠 **Exam tip:** Know these 4 principles **by name**.

### Free Services & AWS Free Tier
- Some orchestration services are free themselves (you pay only for underlying resources): IAM, VPC construct, Organizations/Consolidated Billing, Elastic Beanstalk, CloudFormation, Auto Scaling.
- **Free Tier changed July 15, 2025:**
  - **New accounts (on/after 7/15/2025):** Free Plan or Paid Plan, both starting with $100 credit (extendable to $200). Free Plan runs up to 6 months or until credits run out, then closes. Paid Plan → standard pay-as-you-go after credits used.
  - **Legacy accounts (before 7/15/2025):** classic 3-part model — Always Free, 12-months-free, short-term trials.
  - Either way: **Always Free** services with permanent monthly limits (Lambda, DynamoDB, SNS) remain free regardless.

> 🧠 **Exam tip:** Older material describes the classic model (still valid for legacy accounts); new accounts now get credit-based Free/Paid Plans instead of an automatic 12-month allowance.

---

## 8. Compute Pricing — EC2

Charged based on: number/type/size of instances, Region, OS, licensing, ELB running time/data processed, detailed (1-min) CloudWatch monitoring (basic 5-min is free).

| Purchase Option | Discount | Best For |
|---|---|---|
| **On-Demand** | None | Unpredictable, short-term, spiky workloads |
| **Reserved Instances** | Up to 72% | Steady-state, predictable workloads (1 or 3 yr) |
| **Spot Instances** | Up to 90% | Fault-tolerant, flexible (batch, big data, CI/CD) — 2-min interruption warning |
| **Dedicated Hosts** | N/A (most expensive) | Licensing tied to physical cores/sockets (BYOL) |

> 🧠 **Exam tip:** On-Demand = unpredictable/short-term. Reserved = steady-state. Spot = flexible/interruptible/cheapest — never for critical/stateful workloads. Dedicated Host = licensing/compliance needing physical server visibility.

---

## 9. Storage Pricing — S3

**Storage classes (highest → lowest cost/GB):**
S3 Express One Zone → S3 Standard → S3 Intelligent-Tiering → S3 Standard-IA → S3 One Zone-IA → S3 Glacier Instant Retrieval → S3 Glacier Flexible Retrieval → S3 Glacier Deep Archive

- **Express One Zone:** single-AZ, single-digit ms latency — hottest data.
- **Intelligent-Tiering:** auto-moves objects between tiers — unpredictable access patterns.
- **Glacier Instant Retrieval:** archive pricing, ms retrieval.
- **Glacier Flexible/Deep Archive:** lowest-cost long-term archive; retrieval minutes → hours.

**Pricing factors:** object count/size (tiered), request number/type, data transfer OUT (IN is free), Transfer Acceleration, lifecycle transitions.

Similar service: **EFS** (pay-per-use, Infrequent Access class, lifecycle rules) — shared file storage vs. object storage.

> 🧠 **Exam tip:** Frequent access → Standard. Unknown pattern → Intelligent-Tiering. Infrequent but fast → Standard-IA / One Zone-IA. Archival needing immediate access → Glacier Instant Retrieval. True cold archive → Glacier Flexible/Deep Archive.

---

## 10. Storage Pricing — EBS

Billed per GB-month provisioned, plus performance charges:

- **gp3/gp2 (General Purpose SSD):** baseline IOPS/throughput included; gp3 allows extra IOPS/throughput for a fee.
- **io2/io1 (Provisioned IOPS SSD):** pay separately for provisioned IOPS — highest-performance workloads (large DBs).
- **st1/sc1 (Magnetic/HDD):** throughput-optimized, priced per GB, no separate IOPS — large sequential workloads (big data, logs), not boot volumes.
- **Snapshots:** billed per GB-month in S3, incremental since last snapshot.
- **Data transfer:** outbound tiered; inbound always free.

---

## 11. Database Pricing — RDS

Billed per hour/second based on:
- Engine (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora)
- Instance class
- Purchase type: On-Demand or Reserved (1/3 yr; all/partial/no upfront)

- **Backup storage:** free up to 100% of provisioned DB storage per Region; beyond that (and manual snapshots after deletion) billed per GB-month.
- **Additional storage** + Provisioned IOPS (if applicable).
- **Deployment type:** Multi-AZ roughly **doubles** storage/I/O cost vs. Single-AZ (synchronous standby).
  - Single-AZ: lower cost, no auto-failover.
  - Multi-AZ: higher cost, auto-failover — standard for production.
- Data transfer: outbound tiered; inbound free.

---

## 12. Content Delivery — CloudFront Pricing

- Pricing varies by **geographic price class** (edge location); usage aggregated by edge location before billing.
- Data transfer out is **tiered** — cost/GB drops with volume.
- Request pricing: per 10,000 HTTP/HTTPS requests (HTTPS = higher rate).
- Can select a **price class** (e.g., North America + Europe only) to limit edge locations and reduce cost, at the expense of latency elsewhere.

---

## 13. Data Transfer Costs — Simplified

- Data transfer **IN** to AWS → always **free**.
- Data transfer **OUT** to internet → main cost driver, **tiered** (lower rate at higher volume).
- Transfer **between AWS services in the same Region** → typically free/cheaper.
- Prefer **private IPs** over public IPs for same-Region traffic — cheaper + lower latency.
- Cost ranking (cheapest → most expensive): same AZ < cross-AZ (same Region) < cross-Region.

> 🧠 **Exam tip:** Trade-off: single-AZ minimizes transfer cost but sacrifices fault tolerance. Multi-AZ costs more (inter-AZ transfer) but is the resilient, exam-recommended design.

---

## 14. Savings Plans

Commit to a **consistent $/hour** for 1 or 3 years; AWS auto-applies the discount to matching usage. Usage above commitment = standard On-Demand rate.

| Type | Discount | Flexibility |
|---|---|---|
| **EC2 Instance Savings Plan** | Up to 72% | Locked to one instance family + Region; size/OS/tenancy can change |
| **Compute Savings Plan** | Up to 66% | Any family/size/OS/tenancy/Region; covers EC2, Fargate, Lambda |
| **SageMaker Savings Plan** | Up to 64% | Any SageMaker ML instance usage |
| **Database Savings Plans** (2024) | — | Select RDS/Aurora usage; 1-yr, no-upfront only |

- Purchased/managed via **Cost Explorer** console (which recommends commitment level).

> 🧠 **Exam tip:** Both Savings Plans and RIs need 1/3-yr commitment. Savings Plans = commit to $/hour spend (flexible). RIs = commit to a specific instance configuration. Savings Plans = generally more flexible & AWS-recommended for compute.

---

## 15. Estimating Costs — Pricing Calculator

**AWS Pricing Calculator** = free, web-based, no AWS account/resources required.

Use cases:
- Model solutions **before** building them
- Explore price points across Regions/configurations
- Review detailed calculation assumptions
- Plan/share/export spend estimates
- Compare pricing models (On-Demand vs. Reserved vs. Savings Plans)

> 🧠 **Exam tip:** **Pricing Calculator** = estimate costs *before* deployment ("what-if"). **Cost Explorer** = analyze costs *after* usage is incurred.

---

## Quick-Reference: "Which Tool Do I Use?"

| Scenario | Answer |
|---|---|
| Isolate environments / contain security incident / separate billing | Multiple accounts + AWS Organizations |
| Block root user or restrict a whole account from a service/Region | SCP |
| Restrict what can be done *to* a resource org-wide | RCP |
| Auto-build a best-practice multi-account landing zone | Control Tower |
| Share existing resources (e.g., Transit Gateway) across accounts | AWS RAM |
| Self-service deploy pre-approved architectures only | Service Catalog |
| Most granular billing dataset for custom analysis/data warehouse | Cost and Usage Reports (CUR) |
| Visual/interactive spend trend analysis | Cost Explorer |
| Simple total-spend threshold alarm | CloudWatch Billing Alarm (us-east-1 only) |
| Advanced budget types + automated actions | AWS Budgets |
| Detect unexpected/unusual spend, no fixed threshold | Cost Anomaly Detection |
| Warn before hitting a service limit | Service Quotas |
| Automated best-practice checks across 6 categories | Trusted Advisor |
| Estimate cost of an architecture before deploying | Pricing Calculator |
