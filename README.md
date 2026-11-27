# AWS Certified Cloud Practitioner (CLF-C02) Study Curriculum and Repository Guide

## Overview

This repository contains a comprehensive, structured study curriculum designed for candidates preparing for the **AWS Certified Cloud Practitioner (CLF-C02)** examination. The materials are organized into modular domains covering foundational cloud concepts, core AWS architectural components, security and compliance frameworks, networking, databases, application integration, and billing and support structures.

---

## Examination Blueprint (CLF-C02)

The AWS Certified Cloud Practitioner exam validates an overall understanding of the AWS Cloud platform, independent of specific technical roles.

| Domain | Exam Weight | Primary Focus |
|---|---|---|
| **Domain 1: Cloud Concepts** | 24% | Cloud value proposition, economics, cloud architecture principles, AWS Well-Architected Framework, and AWS Cloud Adoption Framework (AWS CAF). |
| **Domain 2: Security and Compliance** | 30% | AWS Shared Responsibility Model, IAM, security services (encryption, threat detection, network protection), compliance resources, and governance. |
| **Domain 3: Cloud Technology and Services** | 34% | AWS Global Infrastructure, core services (Compute, Storage, Database, Networking), serverless paradigms, AI/ML, and analytics offerings. |
| **Domain 4: Billing, Pricing, and Support** | 12% | AWS pricing models, cost management and estimation tools, billing structures (AWS Organizations), and AWS Support tier features. |

### Exam Details
- **Question Format:** Multiple choice (one correct response out of four options) and multiple response (two or more correct responses out of five or more options).
- **Total Questions:** 65 questions (50 scored, 15 unscored diagnostic questions).
- **Time Limit:** 90 minutes.
- **Passing Score:** 700 / 1000 (scaled score from 100 to 1000).
- **Prerequisites:** None required; foundational understanding of IT services and general cloud computing concepts is recommended.

---

## Repository Curriculum and File Index

All learning notes, architectural comparisons, summaries, and self-assessment resources in this repository are categorized as follows:

```
.
|-- Billing-Pricing-Support/
|   `-- AWS-CLF-C02-Cost-Management-Summary.md    # Multi-account governance, cost tools, support tiers
|-- Cloud-Computing-Fundamentals-and-Concepts/
|   `-- notes.md                                  # Cloud characteristics, advantages, models, CAF, Well-Architected
|-- Cloud-Technology-and-Services/
|   `-- Cloud_Technology_and_Services_Summary_EN.md # High-yield summary of core cloud technologies and governance
|-- Databases/
|   `-- aws_databases_study_notes.md              # Relational vs NoSQL, RDS, Aurora, DynamoDB, ElastiCache, Redshift
|-- EC2/
|   |-- aws-ec2-study-notes.md                    # Compute deep-dive, instance types, pricing, EBS, EFS, AMIs
|   |-- summary/
|   |   `-- 1- EC2 - AWS Practitioner.pdf         # Condensed presentation summary of EC2
|   `-- quizes/
|       |-- quiz_1.html                           # Interactive browser-based quiz: EC2 fundamentals and storage
|       `-- quiz_2.html                           # Interactive browser-based quiz: Advanced EC2, networking, and scaling
|-- ELB/
|   `-- aws-scalability-notes.md                  # Vertical vs Horizontal scaling, ALB, NLB, GLB, Auto Scaling Groups
|-- Further-AWS-Services/
|   `-- Further_AWS_Services_Summary_EN.md        # Security services, STS, Cognito, SQS, SNS, EventBridge, AI/ML
|-- S3/
|   `-- aws-s3-study-notes.md                     # Object storage, storage classes, lifecycle policies, Snow Family
|-- VPC/
|   `-- aws-vpc-networking-notes.md               # Subnets, Route Tables, IGW, NAT, Security Groups vs NACLs, Peering
`-- README.md                                     # Master curriculum guide and core knowledge syllabus
```

### Module Link Reference

1. [Cloud Computing Fundamentals and Concepts](Cloud-Computing-Fundamentals-and-Concepts/notes.md)
   - Cloud definition, 5 NIST characteristics, 6 business advantages, deployment models, AWS Global Infrastructure, Shared Responsibility Model, AWS Cloud Adoption Framework (CAF), and AWS Well-Architected Framework (6 pillars).
2. [Amazon EC2 and Compute Architecture](EC2/aws-ec2-study-notes.md)
   - Instance sizing, User Data automation, purchasing options (On-Demand, Reserved, Spot, Savings Plans, Dedicated Hosts), EBS volumes, EBS snapshots, AMIs, Instance Store, EFS, and Amazon FSx.
   - Accompanying visual guide: [EC2 Summary PDF](EC2/summary/1-%20EC2%20-%20AWS%20Practitioner.pdf).
   - Self-tests: [EC2 Quiz 1](EC2/quizes/quiz_1.html) and [EC2 Quiz 2](EC2/quizes/quiz_2.html).
3. [Scalability, High Availability, and Elastic Load Balancing](ELB/aws-scalability-notes.md)
   - Vertical vs. horizontal scalability, Application Load Balancer (ALB), Network Load Balancer (NLB), Gateway Load Balancer (GLB), Auto Scaling Groups (ASG), and dynamic scaling policies.
4. [Amazon VPC and Cloud Networking](VPC/aws-vpc-networking-notes.md)
   - VPC design, Public vs. Private Subnets, Route Tables, Internet Gateways (IGW), NAT Gateways vs. NAT Instances, Security Groups vs. Network ACLs, VPC Peering, and VPC Endpoints.
5. [Amazon S3 and Cloud Storage](S3/aws-s3-study-notes.md)
   - Object storage architecture, bucket policies, IAM integration, S3 storage tiers (Standard, Intelligent-Tiering, Standard-IA, One Zone-IA, Glacier tiers), Lifecycle Policies, Versioning, and the AWS Snow Family.
6. [AWS Managed Databases](Databases/aws_databases_study_notes.md)
   - Relational (SQL) vs. Non-Relational (NoSQL), Amazon RDS, Amazon Aurora, Amazon DynamoDB, Amazon ElastiCache, Amazon Redshift, and specialized database engines.
7. [Billing, Pricing, and Account Governance](Billing-Pricing-Support/AWS-CLF-C02-Cost-Management-Summary.md)
   - AWS Organizations, Consolidated Billing, Service Control Policies (SCPs), AWS Pricing Calculator, Cost Explorer, AWS Budgets, Cost Anomaly Detection, AWS Support Plans comparison, and AWS Trusted Advisor.
8. [Cloud Technology and Services Summary](Cloud-Technology-and-Services/Cloud_Technology_and_Services_Summary_EN.md)
   - Executive exam summary of multi-account strategy, Control Tower, compute paradigms, and system monitoring.
9. [Further AWS Services and Advanced Integrations](Further-AWS-Services/Further_AWS_Services_Summary_EN.md)
   - AWS STS, Amazon Cognito, AWS KMS, CloudHSM, Secrets Manager, AWS Shield, AWS WAF, Amazon GuardDuty, Amazon Inspector, Amazon Macie, CloudWatch vs. CloudTrail, and messaging/decoupling services (SQS, SNS, EventBridge).

---

## Domain Syntheses and Study Guide

### 1. Cloud Computing Fundamentals and Economics

#### 5 Essential Characteristics of Cloud Computing (NIST SP 800-145)
- **On-Demand Self-Service:** Provision computing capabilities automatically without requiring human interaction with the service provider.
- **Broad Network Access:** Capabilities are available over the network and accessed through standardized mechanisms on diverse platforms (mobile phones, tablets, laptops, workstations).
- **Resource Pooling:** Multi-tenant model where physical and virtual resources are dynamically assigned and reassigned according to consumer demand, with location independence.
- **Rapid Elasticity:** Capabilities can be elastically provisioned and released, in some cases automatically, to scale outward and inward rapidly with demand.
- **Measured Service:** Resource usage can be monitored, controlled, and reported, providing transparency for both the provider and consumer (pay-as-you-go).

#### 6 Advantages of Cloud Computing
1. **Trade Capital Expense (CapEx) for Variable Expense (OpEx):** Pay only for resources consumed rather than investing heavily upfront in data centers and servers.
2. **Benefit from Massive Economies of Scale:** Aggregate customer usage results in higher economies of scale, translating into lower pay-as-you-go prices.
3. **Stop Guessing Capacity:** Eliminate capacity limits by scaling up or down automatically based on actual real-time application traffic.
4. **Increase Speed and Agility:** Reduce resource provisioning time from weeks or months to minutes.
5. **Stop Spending Money Running and Maintaining Data Centers:** Focus business efforts on differentiation and customer value rather than infrastructure maintenance.
6. **Go Global in Minutes:** Deploy applications across multiple AWS Regions worldwide with low latency and minimal cost.

#### Cloud Service Models
- **Infrastructure as a Service (IaaS):** Highest level of user flexibility and management control over IT resources (e.g., Amazon EC2, Amazon VPC).
- **Platform as a Service (PaaS):** Removes the need to manage the underlying infrastructure (hardware, OS, patches), allowing focus on application deployment (e.g., AWS Elastic Beanstalk).
- **Software as a Service (SaaS):** Complete end-user software application managed and run by the service provider (e.g., AWS Organizations, Amazon QuickSight).

#### Cloud Deployment Models
- **Public Cloud:** Applications fully deployed in the cloud; all components run in the cloud.
- **Hybrid Cloud:** Connected infrastructure bridging on-premises facilities and cloud resources (via AWS Direct Connect, AWS Site-to-Site VPN, or AWS Outposts).
- **Private Cloud (On-Premises):** Dedicated infrastructure hosted within an organization's private data center using virtualization technologies.

---

### 2. AWS Global Infrastructure and Reliability

```
+-------------------------------------------------------------------------------+
|                                  AWS Region                                   |
| (Geographic area containing 2 or more isolated Availability Zones)            |
|                                                                               |
|  +---------------------------+             +-------------------------------+  |
|  |   Availability Zone A     |             |     Availability Zone B       |  |
|  |  (1+ physical data centers|  Low-latency|    (1+ physical data centers  |  |
|  |   isolated power/network) |<----------->|     isolated power/network)   |  |
|  +---------------------------+  Fiber Link +-------------------------------+  |
+-------------------------------------------------------------------------------+
                                        ^
                                        | Low-latency backbone
                                        v
+-------------------------------------------------------------------------------+
|                       Points of Presence (Edge Network)                       |
|   Edge Locations & Regional Edge Caches (Amazon CloudFront, Route 53, Shield) |
+-------------------------------------------------------------------------------+
```

- **Region:** A distinct physical geographical location around the world (e.g., `us-east-1`, `eu-west-1`). Each region consists of a minimum of three isolated and physically separated Availability Zones (AZs).
- **Availability Zone (AZ):** One or more discrete data centers, each with redundant power, networking, and connectivity, housed in separate facilities. AZs are connected through low-latency, high-throughput, private optical networking.
- **Edge Locations (Points of Presence):** Worldwide data centers used by Amazon CloudFront (Content Delivery Network), Amazon Route 53 (DNS), and AWS Global Accelerator to deliver data, videos, applications, and APIs to users with low latency.
- **AWS Outposts:** Fully managed service that extends AWS infrastructure, native services, APIs, and tools to virtually any customer data center, co-location space, or on-premises facility.
- **AWS Local Zones:** Places compute, storage, database, and other select services closer to end users in large population centers where no AWS Region currently exists.
- **AWS Wavelength:** Embeds AWS compute and storage services within 5G telecommunication networks to deliver ultra-low latency applications for mobile devices.

---

### 3. AWS Well-Architected Framework and Cloud Adoption Framework (CAF)

#### The 6 Pillars of the AWS Well-Architected Framework
1. **Operational Excellence:** Focuses on running and monitoring systems to deliver business value and continually improving processes and procedures (e.g., performing operations as code, making frequent and reversible changes).
2. **Security:** Focuses on protecting information, systems, and assets while delivering business value through risk assessments and mitigation strategies (e.g., applying security at all layers, principle of least privilege).
3. **Reliability:** Focuses on ensuring a workload performs its intended function correctly and consistently when expected to do so (e.g., testing recovery procedures, automatically recovering from failure, scaling horizontally).
4. **Performance Efficiency:** Focuses on using computing resources efficiently to meet requirements and maintaining efficiency as demand changes and technologies evolve (e.g., democratizing advanced technologies, going serverless).
5. **Cost Optimization:** Focuses on avoiding unnecessary costs (e.g., adopting a consumption model, measuring overall efficiency, analyzing expenditure).
6. **Sustainability:** Focuses on minimizing the environmental impacts of running cloud workloads (e.g., maximizing utilization, adopting efficient hardware and software architectures).

#### The AWS Cloud Adoption Framework (AWS CAF) Perspectives
- **Business Perspectives (Focus on business outcomes):**
  - *Business:* Ensures cloud investments align with business strategies and measurable outcomes.
  - *People:* Bridges the gap between technology and human resources, evolving organizational structures and skills.
  - *Governance:* Aligns cloud initiatives with business governance while managing organizational risks.
- **Technical Perspectives (Focus on technical implementation):**
  - *Platform:* Modernizes enterprise workloads and optimizes cloud architecture.
  - *Security:* Ensures compliance, data protection, confidentiality, and integrity.
  - *Operations:* Delivers cloud services at the level of reliability agreed upon with the business.

---

### 4. Security, Identity, and Compliance

#### The AWS Shared Responsibility Model

```
+---------------------------------------------------------------------------------------+
|                         CUSTOMER RESPONSIBILITY ("SECURITY IN THE CLOUD")             |
|                                                                                       |
|  - Customer Data (Classification, Encryption at Rest & in Transit)                   |
|  - Platform, Applications, Identity & Access Management (IAM, MFA, User Passwords)    |
|  - Operating System Configuration, Network & Firewall Configuration (Security Groups)|
+---------------------------------------------------------------------------------------+
======================================= DIVIDING LINE ===================================
+---------------------------------------------------------------------------------------+
|                           AWS RESPONSIBILITY ("SECURITY OF THE CLOUD")                |
|                                                                                       |
|  - Software: Compute, Storage, Database, Networking Infrastructure Software           |
|  - Hardware & AWS Global Infrastructure: Regions, Availability Zones, Edge Locations  |
|  - Physical Security of Facilities, Power, Cooling, Environmental Controls            |
+---------------------------------------------------------------------------------------+
```

#### Shared Responsibility by Service Abstraction Level
| Service Type | AWS Manages | Customer Manages | Examples |
|---|---|---|---|
| **Infrastructure Services (IaaS)** | Physical facilities, physical hardware, hypervisor | OS installation/patching, application software, firewall rules (Security Groups), data encryption | Amazon EC2, Amazon EBS, Amazon VPC |
| **Container / Managed Services (PaaS)** | Physical infrastructure, OS installation, platform maintenance, database engine patches | Application code, database schema, data encryption, network-level access control rules | Amazon RDS, AWS Elastic Beanstalk |
| **Abstracted / Serverless Services (SaaS)** | Physical infrastructure, OS, scaling, high availability, platform execution environment | IAM permissions, customer data, API calls, client-side encryption | Amazon S3, Amazon DynamoDB, AWS Lambda |

#### Key Security Services Matrix
- **AWS Identity and Access Management (IAM):** Centralized control over authentication and authorization. Key elements: Users, Groups, Roles (assumed temporarily), Policies (JSON documents specifying Allow/Deny). Best practice: Grant Least Privilege and enforce Multi-Factor Authentication (MFA).
- **AWS Security Token Service (STS):** Requests temporary, limited-privilege credentials for users, cross-account access, or federated identities.
- **AWS Key Management Service (KMS):** Managed service for creating and controlling cryptographic keys to encrypt data across AWS services.
- **AWS CloudHSM:** Dedicated hardware security module under dedicated customer control for regulatory compliance (FIPS 140-2 Level 3).
- **AWS Secrets Manager:** Manages, retrieves, and automatically rotates database credentials, API keys, and other secrets.
- **AWS Shield:** Managed Distributed Denial of Service (DDoS) protection. Standard tier is free and automatic for all customers; Advanced tier offers specialized mitigation, cost protection against scaling spikes, and 24/7 access to the AWS Shield Response Team (SRT).
- **AWS WAF (Web Application Firewall):** Filters HTTP(S) web traffic against common exploits such as SQL Injection (SQLi) and Cross-Site Scripting (XSS).
- **Amazon GuardDuty:** Intelligent threat detection service that continuously monitors VPC flow logs, DNS logs, and CloudTrail management events using machine learning.
- **Amazon Inspector:** Automated vulnerability management service that scans EC2 instances, container images, and Lambda functions for software vulnerabilities and unintended network exposure.
- **Amazon Macie:** Fully managed data security and privacy service that uses machine learning and pattern matching to discover, classify, and protect sensitive data (such as PII) stored in Amazon S3.
- **AWS Artifact:** Central portal providing on-demand access to AWS compliance reports (e.g., SOC, PCI-DSS, ISO) and AWS agreements (e.g., Business Associate Addendum - BAA for HIPAA).
- **AWS Audit Manager:** Continuously audits AWS usage to simplify risk assessment and regulatory compliance.

---

### 5. Compute, Storage, and Networking Comparison

#### EC2 Pricing Models
| Model | Commitment | Discount Level | Primary Use Case |
|---|---|---|---|
| **On-Demand** | None (pay per second/hour) | Baseline (0%) | Short-term, irregular workloads with unpredictable traffic; development and testing. |
| **Savings Plans** | 1 or 3 years ($/hr spend commitment) | Up to 72% | Flexible compute usage across EC2, Fargate, and Lambda with consistent hourly spend. |
| **Reserved Instances (RI)** | 1 or 3 years (steady-state usage) | Up to 72% | Steady-state or predictable usage workloads; Standard (can sell/modify) vs. Convertible (can change instance family). |
| **Spot Instances** | None (spare AWS capacity) | Up to 90% | Fault-tolerant, flexible, interruptible workloads (batch processing, data analysis, rendering). AWS can reclaim with a 2-minute warning. |
| **Dedicated Hosts** | 1 or 3 years or On-Demand | Lowest discount | Strict compliance, regulatory requirements, or server-bound software licenses (BYOL). Physical server dedicated entirely to the customer. |
| **Dedicated Instances** | None or Reserved | Higher cost than standard | Dedicated physical hardware for the customer at the instance level (no hardware sharing with other AWS accounts). |

#### Storage Options Comparison
| Storage Type | AWS Service | Data Structure | Multi-Instance Access | Persistence | Typical Use Cases |
|---|---|---|---|---|---|
| **Block Storage** | Amazon EBS | Unformatted blocks (file system required) | No (single instance; EBS Multi-Attach limited to io1/io2 in same AZ) | Persistent (independent of EC2 lifecycle) | OS boot drives, relational databases, transactional applications. |
| **Ephemeral Block** | EC2 Instance Store | Raw physical blocks on host server | Single instance | Ephemeral (lost on stop/terminate) | High-performance caches, scratch buffers, temporary processing data. |
| **File Storage** | Amazon EFS | Managed POSIX file system | Yes (hundreds of instances across multiple AZs) | Persistent | Shared content directories, web serving, big data analytics. |
| **Object Storage** | Amazon S3 | Objects with metadata and unique keys | Yes (HTTP/HTTPS REST API worldwide) | Persistent (99.999999999% durability) | Backups, archives, static web hosting, media assets, data lakes. |

#### Amazon S3 Storage Classes
| Tier | Availability | Minimum Storage Duration | Retrieval Fee | Primary Use Case |
|---|---|---|---|---|
| **S3 Standard** | 99.99% | None | None | Frequently accessed data, active websites, mobile apps. |
| **S3 Intelligent-Tiering** | 99.9% | None (monitoring fee applies) | None | Data with unknown, changing, or unpredictable access patterns. |
| **S3 Standard-Infrequent Access (IA)** | 99.9% | 30 days | Per GB retrieved | Long-lived data accessed less frequently, but requiring immediate access when needed. |
| **S3 One Zone-IA** | 99.5% (Single AZ) | 30 days | Per GB retrieved | Secondary backup copies or easily re-creatable data. Lower cost; no multi-AZ resilience. |
| **S3 Glacier Instant Retrieval** | 99.9% | 90 days | Per GB retrieved | Archive data accessed once a quarter with millisecond retrieval. |
| **S3 Glacier Flexible Retrieval** | 99.99% | 90 days | Per GB retrieved | Archive data with retrieval times ranging from minutes (Expedited: 1-5 min) to hours (Standard: 3-5 hrs; Bulk: 5-12 hrs). |
| **S3 Glacier Deep Archive** | 99.99% | 180 days | Per GB retrieved | Lowest-cost cloud storage for long-term data retention (7-10+ years); retrieval in 12-48 hours. |

#### Networking: Security Groups vs. Network Access Control Lists (NACLs)
| Property | Security Group | Network Access Control List (NACL) |
|---|---|---|
| **Operating Layer** | Instance level (operates at virtual network interface) | Subnet level (boundary protection for the whole subnet) |
| **Rule Types** | **Allow rules only** (everything else denied by default) | **Allow and Deny rules** |
| **State Tracking** | **Stateful:** Return traffic is automatically allowed regardless of inbound/outbound rules | **Stateless:** Return traffic must be explicitly allowed by corresponding rules |
| **Rule Processing** | All rules evaluated simultaneously before traffic is permitted | Rules evaluated in sequential numerical order (lowest numbered rule evaluated first) |
| **Default Setting** | Default SG allows all outbound, denies all inbound | Default NACL allows all inbound and outbound traffic; Custom NACL denies all until configured |

---

### 6. Databases and Analytics Services

| Database Service | Type | Key Features | Exam Indicator Keywords |
|---|---|---|---|
| **Amazon RDS** | Relational (SQL) | Managed MySQL, PostgreSQL, MariaDB, Oracle, SQL Server. Supports Multi-AZ (Disaster Recovery/High Availability) and Read Replicas (Read Scaling). | Traditional relational schema, joins, transactions, OLTP. |
| **Amazon Aurora** | Relational (Cloud-native SQL) | Up to 5x throughput of standard MySQL, 3x PostgreSQL. Automatically replicates 6 copies of data across 3 AZs. Continuous backups to S3. | Enterprise relational database, high performance, auto-repairing storage. |
| **Amazon DynamoDB** | Non-Relational (NoSQL) | Fully managed key-value and document database. Single-digit millisecond latency at any scale. Serverless with automatic partitioning. | Key-value store, massive scale, predictable low latency, serverless database. |
| **Amazon ElastiCache** | In-Memory Cache | Supports Redis and Memcached. Offloads read load from databases to deliver sub-millisecond read response times. | In-memory cache, read-heavy workloads, session storage. |
| **Amazon Redshift** | Data Warehouse (OLAP) | Columnar storage, SQL-based analytics across petabytes of structured and semi-structured data. | Data warehousing, complex analytical queries, BI reporting. |
| **Amazon DocumentDB** | Document (NoSQL) | Fully managed MongoDB-compatible document database. | JSON documents, MongoDB compatibility. |
| **Amazon Neptune** | Graph | Highly available graph database optimized for storing and querying complex connected datasets. | Social networks, fraud detection, recommendation engines, knowledge graphs. |

---

### 7. Billing, Pricing, and Support Frameworks

#### Account Management and Governance
- **AWS Organizations:** Centralized management for multiple AWS accounts. Key benefits include:
  - *Consolidated Billing:* Single bill for all accounts, aggregated usage across accounts to qualify for volume pricing discounts, and pooling of Reserved Instances / Savings Plans.
  - *Service Control Policies (SCPs):* Organization-wide guardrails that define the maximum permissions member accounts can grant (cannot be overridden by member account administrators; does not apply to the Management account).
- **AWS Control Tower:** Automates the setup of a well-architected multi-account environment ("Landing Zone") using best-practice blueprints and pre-configured guardrails.

#### Cost Management and Estimation Tools
- **AWS Pricing Calculator:** Web-based planning tool to estimate costs for planned architectures before provisioning resources.
- **AWS Cost Explorer:** Interactive visualization tool used to analyze, visualize, and forecast historical and current usage trends (up to the past 12 months, with forecasts for the next 12 months).
- **AWS Budgets:** Sets custom cost and usage limits that trigger proactive alerts (via SNS or email) when costs exceed, or are forecasted to exceed, budgeted thresholds.
- **AWS Cost and Usage Report (CUR):** The most comprehensive dataset of AWS cost and usage data, delivered as CSV files into an Amazon S3 bucket.
- **AWS Cost Anomaly Detection:** Uses machine learning to detect unusual spending spikes and root causes.

#### AWS Support Plans Comparison
| Feature | Basic | Developer | Business | Enterprise On-Ramp | Enterprise |
|---|---|---|---|---|---|
| **Cost** | Free (included) | Greater of $29/mo or 3% of monthly spend | Greater of $100/mo or percentage tiered | Greater of $5,500/mo or 10% of monthly spend | Greater of $15,000/mo or percentage tiered |
| **Customer Service** | 24/7 billing and account support | 24/7 billing and account support | 24/7 billing and account support | 24/7 billing and account support | 24/7 billing and account support |
| **Technical Support Contacts** | None | 1 primary contact | Unlimited contacts | Unlimited contacts | Unlimited contacts |
| **Communication Channels** | None (Docs/Forums only) | Web-based support (business hours) | 24/7 phone, chat, and web | 24/7 phone, chat, and web | 24/7 phone, chat, and web |
| **Response Times** | N/A | General guidance < 24 hrs; System impaired < 12 hrs | Production impaired < 4 hrs; Production system down < 1 hr | Production system down < 1 hr; Business-critical system down < 30 min | Production system down < 1 hr; Business-critical system down < 15 min |
| **Trusted Advisor Checks** | 7 core security & service quota checks | 7 core security & service quota checks | Full set of checks (all pillars) | Full set of checks (all pillars) | Full set of checks (all pillars) |
| **Architectural Guidance** | General best practices | Building-block guidance | Contextual to your use cases | Consultative review and guidance | Consultative review and guidance |
| **Dedicated Technical Contact** | None | None | None | Technical Account Manager (TAM) pool | Dedicated Technical Account Manager (TAM) |
| **Specialized Programs** | None | None | None | Infrastructure Event Management (IEM) for additional fee | Infrastructure Event Management (IEM) included |

#### Advisory Services
- **AWS Trusted Advisor:** Real-time guidance across 6 categories: Cost Optimization, Performance, Security, Fault Tolerance, Service Quotas, and Operational Excellence.
- **AWS Compute Optimizer:** Machine learning-powered recommendations to right-size EC2 instances, EBS volumes, Lambda functions, and Auto Scaling groups to eliminate over-provisioning and under-provisioning.

---

## High-Yield Exam Keyword Associations

When reviewing exam questions, specific phrasing strongly hints at designated AWS services:

| Exam Trigger Phrase | Correct AWS Service Target |
|---|---|
| "Consolidate billing across multiple accounts and enforce guardrails" | **AWS Organizations (with SCPs)** |
| "Predict or forecast costs over the next 12 months" | **AWS Cost Explorer** |
| "Send alert when monthly spend is projected to exceed threshold" | **AWS Budgets** |
| "Estimate cost of an infrastructure architecture prior to building" | **AWS Pricing Calculator** |
| "Download compliance reports and audit certifications (SOC, PCI)" | **AWS Artifact** |
| "Temporarily delegate access without sharing long-term IAM credentials" | **AWS Security Token Service (STS)** |
| "Managed DDoS protection with access to 24/7 response team" | **AWS Shield Advanced** |
| "Filter web application traffic against SQL injection and cross-site scripting" | **AWS WAF** |
| "Discover and classify sensitive data (PII) stored in S3" | **Amazon Macie** |
| "Threat detection analyzing VPC flow logs, DNS logs, and CloudTrail" | **Amazon GuardDuty** |
| "Automated vulnerability scanning of EC2 instances and container images" | **Amazon Inspector** |
| "Record and track configuration history and evaluate compliance against rules" | **AWS Config** |
| "Record API calls, user actions, and management changes across account" | **AWS CloudTrail** |
| "Monitor metrics, CPU utilization, operational alarms, and application logs" | **Amazon CloudWatch** |
| "Stateless firewall operating at the subnet boundary with permit/deny rules" | **Network Access Control List (NACL)** |
| "Stateful firewall operating at the individual instance network interface level" | **Security Group** |
| "Connect private on-premises data center to AWS over a dedicated, private fiber connection" | **AWS Direct Connect** |
| "Low-cost long-term object archival with retrieval time of 12 to 48 hours" | **Amazon S3 Glacier Deep Archive** |
| "High-throughput shared file system accessible by multiple Linux EC2 instances" | **Amazon EFS** |
| "Key-value NoSQL database offering single-digit millisecond response times" | **Amazon DynamoDB** |
| "Relational database compatible with MySQL/PostgreSQL with automatic replication across 3 AZs" | **Amazon Aurora** |
| "Dedicated individual point of contact providing technical guidance (TAM)" | **AWS Enterprise Support Plan** |
| "Recommendations to right-size EC2 instance families to eliminate wasted compute" | **AWS Compute Optimizer** |

---

## Interactive Self-Assessment Resources

This repository includes interactive self-assessment tools to test your conceptual knowledge and readiness:

### 1. EC2 Fundamentals and Storage Quiz
- **Path:** [`EC2/quizes/quiz_1.html`](EC2/quizes/quiz_1.html)
- **Topics Covered:** Virtualization basics, instance purchasing options (On-Demand, Spot, Reserved), EBS volume performance, and user data automation.
- **Usage:** Open the HTML file in any modern web browser to take the interactive assessment.

### 2. Advanced EC2, Networking, and Scalability Quiz
- **Path:** [`EC2/quizes/quiz_2.html`](EC2/quizes/quiz_2.html)
- **Topics Covered:** Security Groups vs. NACLs, Elastic Load Balancers (ALB, NLB), Auto Scaling Groups, AMIs, and cross-AZ high availability architectures.
- **Usage:** Open the HTML file in any modern web browser to take the interactive assessment.

---

## Recommended Study Roadmap

### Phase 1: Foundational Concepts and Global Architecture (Days 1-3)
- Review [Cloud-Computing-Fundamentals-and-Concepts/notes.md](Cloud-Computing-Fundamentals-and-Concepts/notes.md).
- Understand NIST cloud characteristics and the 6 business advantages of cloud migration.
- Master the AWS Global Infrastructure layout: Regions vs. Availability Zones vs. Edge Locations.
- Study the 6 pillars of the AWS Well-Architected Framework and the 6 perspectives of the AWS CAF.

### Phase 2: Security, Governance, and Access Control (Days 4-7)
- Study the Shared Responsibility Model until you can immediately categorize any responsibility.
- Review IAM fundamentals (Users, Groups, Roles, Policies, MFA, Principle of Least Privilege).
- Study [Further-AWS-Services/Further_AWS_Services_Summary_EN.md](Further-AWS-Services/Further_AWS_Services_Summary_EN.md) for security and identity services (STS, Cognito, KMS, Shield, WAF, GuardDuty, Macie, Inspector).
- Differentiate between auditing and monitoring tools: CloudWatch vs. CloudTrail vs. AWS Config.

### Phase 3: Core AWS Infrastructure - Compute, Storage, and Networking (Days 8-11)
- Read [EC2/aws-ec2-study-notes.md](EC2/aws-ec2-study-notes.md) and review the [EC2 Summary PDF](EC2/summary/1-%20EC2%20-%20AWS%20Practitioner.pdf).
- Understand EC2 instance types and pricing models (On-Demand vs. Spot vs. Reserved vs. Savings Plans).
- Read [S3/aws-s3-study-notes.md](S3/aws-s3-study-notes.md) and memorize all S3 storage classes, retrieval latencies, and lifecycle transitions.
- Read [ELB/aws-scalability-notes.md](ELB/aws-scalability-notes.md) for load balancers and auto-scaling mechanics.
- Read [VPC/aws-vpc-networking-notes.md](VPC/aws-vpc-networking-notes.md) to master subnets, route tables, IGW, NAT, and Security Groups vs. NACLs.
- Complete [EC2/quizes/quiz_1.html](EC2/quizes/quiz_1.html) and [EC2/quizes/quiz_2.html](EC2/quizes/quiz_2.html).

### Phase 4: Databases, Integrations, and Billing (Days 12-14)
- Read [Databases/aws_databases_study_notes.md](Databases/aws_databases_study_notes.md) for SQL vs. NoSQL, RDS, Aurora, and DynamoDB.
- Read [Billing-Pricing-Support/AWS-CLF-C02-Cost-Management-Summary.md](Billing-Pricing-Support/AWS-CLF-C02-Cost-Management-Summary.md).
- Memorize the AWS Support Plans comparison table (response times, TAM availability, Trusted Advisor coverage).
- Review AWS Organizations (Consolidated Billing and SCPs).
- Run through the High-Yield Exam Keyword Associations table for final revision.

---

## Official AWS References and Documentation

- [AWS Certified Cloud Practitioner (CLF-C02) Official Exam Guide](https://d1.awsstatic.com/training-and-certification/docs-cloud-practitioner/AWS-Certified-Cloud-Practitioner_Exam-Guide.pdf)
- [AWS Well-Architected Framework Whitepaper](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [Overview of Amazon Web Services Whitepaper](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/introduction.html)
- [How AWS Pricing Works Whitepaper](https://docs.aws.amazon.com/whitepapers/latest/how-aws-pricing-works/welcome.html)
- [AWS Support Plans Documentation](https://aws.amazon.com/premiumsupport/plans/)
