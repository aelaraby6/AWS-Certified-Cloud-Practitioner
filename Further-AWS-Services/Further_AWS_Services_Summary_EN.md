# Further AWS Services — Exam Summary

---

## 1. AWS STS (Security Token Service)

- Creates **temporary security credentials** (access key ID, secret access key, session token) instead of long-lived IAM user credentials
- Short-lived by design — session duration configurable, typically **15 minutes up to 12 hours max**, depending on how the role is assumed
- **Core use cases:**
  - Assuming an IAM role (same-account or cross-account)
  - Identity federation (corporate directory, Google, Facebook → temporary AWS access via SAML/OIDC)
  - Granting EC2 instances or Lambda functions temporary access via an instance profile / execution role

> 🧠 **Exam Tip:** "Temporary credentials that expire" or "cross-account access without sharing long-term keys" → **STS**. It's a free, global service — no need to create IAM users to hand out these credentials.

---

## 2. Amazon Cognito

- Manages sign-up, sign-in, and access control for your **customer-facing** web/mobile apps — scales to millions of users
- **User Pools:** authentication | **Identity Pools:** hand out temporary AWS credentials (via STS) for authorization
- Supports social IdPs (Google, Facebook, Amazon) and enterprise identity via SAML/OIDC, plus MFA and adaptive authentication

> 🧠 **Exam Tip:** "IAM vs Cognito" is a classic distractor. **IAM** = access for your AWS environment (employees/services). **Cognito** = identity for the end-users of the app you built.
>
> **Anti-pattern:** never create individual IAM users for app end users — doesn't scale, hard to secure, not what IAM is designed for.

---

## 3. Microsoft Active Directory (AD)

- AD Domain Services (AD DS) = a Windows Server role providing directory services
- A database of objects: user accounts, computers, printers, file shares, security groups — the identity backbone of Windows corporate environments
- Enables centralized security management: create accounts/groups once, assign permissions network-wide from a single place
- AWS offers several ways to bring AD into the cloud (see AWS Directory Service below) instead of running your own domain controllers on EC2

---

## 4. AWS Directory Service

### AWS Managed Microsoft AD
- Real Microsoft AD, managed by AWS, on AWS-owned domain controllers
- Create/manage users locally, supports MFA, can establish **trust relationships** with your on-prem AD
- Right choice for genuine AD features (Group Policy, LDAP, Kerberos) or AD-dependent workloads (SharePoint, SQL Server) natively in AWS

### AD Connector
- A directory **gateway/proxy** that redirects authentication requests to your existing on-prem AD
- Supports MFA, but **stores no users itself**
- Users/groups stay managed entirely on-prem — nothing duplicated in AWS

### Simple AD
- Standalone, AD-compatible directory (Samba-based, **not** real Microsoft AD) for basic needs like WorkSpaces or simple LDAP use cases
- **Cannot** establish trust relationships with on-prem AD
- ⚠️ **Current status (mid-2026):** AWS has closed Simple AD to new customers, moving toward maintenance mode. New builds are steered toward Managed Microsoft AD or AD Connector instead. Existing directories keep running.

> 🧠 **Exam Tip:** The differentiator is usually "does this need to sync with our on-prem AD?" **Managed Microsoft AD** (trust) and **AD Connector** (proxy, no local storage) both connect to on-prem; **Simple AD does not**.

---

## 5. AWS IAM Identity Center

- Current name for what was previously **AWS SSO** (renamed 2022) — recognize both names
- One login for all AWS accounts under AWS Organizations, using centrally-defined **permission sets** instead of separate IAM credentials per account
- Also provides SSO to business apps (Salesforce, Box, Microsoft 365) for any SAML 2.0-enabled app
- Identity sources: built-in Identity Center directory, or external IdP (AD via Directory Service, Okta, Microsoft Entra ID/Azure AD, OneLogin)
- **Free** to use — no additional charge

> 🧠 **Exam Tip:** "Centralized login across multiple AWS accounts" or "permission sets" → **IAM Identity Center**. "Temporary credentials for a specific task/role" → **STS**. "My app's end users" → **Cognito**.

---

## 6. Amazon WorkSpaces

- Managed **Desktop as a Service (DaaS)** — a full virtual desktop, not just an app
- Provision persistent Windows or Amazon Linux desktops without physical hardware
- Eliminates the burden of managing on-prem VDI (no patching/capacity-planning VDI servers)
- Scales from a handful to tens of thousands of users, provisioned in minutes
- Secures data at rest and in transit, integrates with AWS KMS for encryption key management
- Flexible pricing: monthly (fixed, always-on) or hourly (occasional use)

> 🧠 **Exam Tip:** "Remote employees need a full desktop experience, centrally managed and secured" → **WorkSpaces**.

---

## 7. Amazon AppStream 2.0

- Fully managed **application streaming** service — streams individual applications, **not** a whole desktop
- Delivers apps to any device without installing/provisioning anything locally
- Renders on AWS, streams pixel-by-pixel into a standard browser — no client software/plugins, no sensitive data leaves AWS
- Common use case: giving contractors/students temporary, secure access to a licensed app (e.g., CAD tool) without installing it on uncontrolled devices

---

## 8. AppStream 2.0 vs WorkSpaces

One of the most commonly tested distinctions in this module.

| WorkSpaces | AppStream 2.0 |
|---|---|
| Fully managed VDI — entire persistent desktop | Streams a single application into a browser |
| Best for: complete workstation experience (own files, multiple apps, personalized settings) | Best for: access to just one app, on any device |
| Can run on-demand or always-on | Pick instance type (CPU/RAM/GPU) per application |

> 🧠 **Anti-pattern to watch for:** if a question only needs "access to one application," WorkSpaces is overkill — **AppStream 2.0** is the leaner, purpose-built answer.

---

## 9. AWS IoT Core

- Connects IoT devices (sensors, appliances, industrial equipment) to the AWS Cloud
- Serverless, secure, scales to billions of devices and trillions of messages
- Devices connect via lightweight, low-power protocols: **MQTT, HTTPS, LoRaWAN** — important for limited bandwidth/battery devices
- **Device Shadow:** a virtual, cached representation of a device's last known state — lets apps "communicate" with a device even while it's offline
- Integrates with Lambda, S3, DynamoDB, Kinesis, SageMaker for storage, analysis, and ML

> 🧠 **Exam Tip:** Device fleets sending telemetry from the field (factory sensors, connected vehicles, smart home devices) → **IoT Core** is the ingestion layer.

---

## 10. AWS AppSync

- Fully managed service for building **GraphQL and Pub/Sub APIs** that sync data across mobile/web apps in real time
- Built on **GraphQL** (created by Facebook, open-sourced 2015) — clients ask for exactly the fields they need in one request, instead of multiple REST round-trips
- Client code and API docs can be auto-generated from the schema
- **Resolvers** connect the GraphQL schema to data sources like DynamoDB, Lambda, Aurora, OpenSearch — one API pulling from several backends
- Built-in **real-time subscriptions** push updates over WebSockets (chat apps, live dashboards, collaborative tools)
- Supports offline data access/sync for unreliable connectivity
- Fine-grained authorization: API keys, IAM, Cognito, OIDC, or Lambda-based custom auth
- AWS Amplify commonly uses AppSync under the hood for its GraphQL API layer
- **AppSync Events:** newer capability for WebSocket-based Pub/Sub APIs without writing a GraphQL schema (e.g., live scores, notifications)

> 🧠 **Exam Tip:** "GraphQL API" or "real-time data sync for mobile apps" → **AppSync**. A plain "REST API" scenario → **API Gateway**.

---

## 11. AWS Amplify

- Toolkit for building, connecting, and hosting full-stack web/mobile apps — aimed at front-end/mobile devs who want cloud backend features without becoming infra experts
- Pre-built building blocks: authentication (Cognito), storage, APIs (REST/GraphQL, often via AppSync), CI/CD hosting, Pub/Sub, analytics, AI/ML predictions, monitoring
- Integrates with Git providers (GitHub, GitLab, Bitbucket) for git-based CI/CD — push code, Amplify builds & deploys automatically

> 🧠 **Exam Tip:** Amplify = the "front-end developer's fast path" to a working cloud backend — it stitches together services like Cognito, AppSync, and S3 rather than being a standalone service.

---

## 12. AWS Application Composer

- Visual, drag-and-drop tool for designing **serverless application architectures** — place and connect resources on a canvas instead of hand-writing templates
- Lowers the barrier to correct IaC by generating it for you (no deep CloudFormation expertise needed to start)
- Generates the underlying CloudFormation (or SAM) template **in real time** as you drag/wire resources
- Supports importing existing CloudFormation/SAM templates to visualize and continue editing

> 🧠 **Exam Tip:** Application Composer is a design/authoring aid — it doesn't replace CloudFormation, it **generates** CloudFormation.

---

## 13. AWS Device Farm

- Fully managed service testing web/mobile apps against **real desktop browsers and real physical mobile devices/tablets** — not just simulators
- Runs tests concurrently across many devices to cut total test time
- Configurable device conditions: GPS location, language/locale, Wi-Fi vs cellular, Bluetooth — to reproduce real usage scenarios

> 🧠 **Exam Tip:** Testing an app across many real device/OS/browser combos before release → **Device Farm**. Don't confuse with CodeBuild/CodePipeline, which build/deploy code rather than test against physical devices.

---

## 14. AWS Backup

- Fully managed service centralizing/automating backups across AWS services (EBS, RDS, DynamoDB, EFS, FSx, Storage Gateway, etc.) from **one console** instead of configuring backups per-service
- On-demand and scheduled backups driven by **backup plans** defined once, applied broadly
- Supports **Point-in-Time Recovery (PITR)** for services like DynamoDB and RDS/Aurora — restore to a specific moment, not just the last full backup
- Backup plans define retention periods and lifecycle rules (e.g., auto-transition older backups to cold storage to cut costs)
- Supports cross-Region backup copies (DR/compliance) and cross-account backup copies via AWS Organizations — backups survive even if one account is compromised

> 🧠 **Exam Tip:** Backing up several different AWS resource types under one consistent, centrally managed policy → **AWS Backup**, not a mix of manual snapshots.

---

## 15. Disaster Recovery Strategies

| Strategy | Description | Cost | Recovery Speed |
|---|---|---|---|
| **Backup and Restore** | Regular backups of data/apps to prevent loss, restored on failure | 🟢 Lowest | 🔴 Slowest |
| **Pilot Light** | Minimal core infrastructure (e.g., DB) kept on standby, scaled up when needed | 🟠 Low | 🟠 Moderate |
| **Warm Standby** | Scaled-down but fully functional copy of production, always running | 🟠 Medium | 🟢 Fast |
| **Multi-Site/Hot Site** | Fully redundant, active secondary site in another geography | 🔴 Highest | 🟢 Fastest, minimal downtime |

---

## 16. AWS Elastic Disaster Recovery (DRS)

- Built on the technology behind **CloudEndure Disaster Recovery** (acquired by AWS and evolved into this native service)
- Fast, reliable recovery of physical, virtual, and cloud-based servers into AWS after data loss/corruption/disaster
- Uses **continuous, block-level replication** to a low-cost staging area in your target Region — data stays current for recovery
- Effectively automates a **Pilot Light → rapid launch** pattern: minimal infra warm in the background, full recovery instances spun up only when actually launched

> 🧠 **Exam Tip:** DRS is AWS's purpose-built, minimal-effort answer for near-continuous DR for on-premises or cross-Region servers, without building a custom replication pipeline.

---

## 17. AWS DataSync

- Automates/accelerates transfer of large amounts of data between on-prem storage and AWS (or between two AWS storage services)
- Supports S3, EFS, FSx (Windows File Server, Lustre, OpenZFS, NetApp ONTAP)
- Scheduled or on-demand tasks; **incremental transfers** after first full copy — only changed data moves

> 🧠 **Exam Tip:** DataSync moves **files/objects** — not block-level server replication (that's DRS/MGN) and not continuous database replication (that's DMS).

---

## 18. AWS Application Discovery Service

- Helps plan a migration by collecting data about existing on-prem servers/apps **before** moving anything
- **Agentless discovery:** collects VM inventory, config, and performance history without installing anything on each server (e.g., via a discovery connector against VMware)
- **Agent-based discovery:** installs a lightweight agent for deeper detail — system config, performance metrics, running processes, network connections between servers (useful for mapping app dependencies)
- All discovered data flows into **AWS Migration Hub**

> 🧠 **Exam Tip:** "We don't know what's running in our data center and need to plan a migration" → **Application Discovery Service** — the first step before Migration Hub or MGN.

---

## 19. AWS Application Migration Service (MGN)

- AWS's primary **lift-and-shift (rehost)** service — recommended path for moving physical/virtual/cloud servers to run natively on AWS with minimal changes
- Continuously replicates source servers to AWS, then converts them to boot/run natively on EC2 at cutover
- Supports a broad range of source platforms, OSes, and databases — no re-architecture needed first
- Designed for minimal downtime at cutover, lower effort/cost than manual rebuilding

> 🧠 **Exam Tip:** MGN and DRS share the same underlying replication technology. **MGN** = planned, one-time migration. **DRS** = ongoing disaster recovery readiness. Don't mix them up.

---

## 20. AWS Migration Evaluator

- Helps build a **data-driven business case** for migrating to AWS — real numbers, not guesswork, for decision-makers
- Installs an **Agentless Collector** capturing server footprint, utilization, and dependencies without agents on individual machines
- Analyzes current state, models a right-sized AWS target state, produces cost projections for the migration plan/executive buy-in

> 🧠 **Exam Tip:** Migration Evaluator = business case & cost projection ("should we migrate, and what will it cost?"). Application Discovery Service = technical inventory ("what do we actually have?"). Often used together but answer different questions.

---

## 21. AWS Migration Hub

- Single dashboard for tracking migration progress across multiple AWS and partner migration tools — discovery data, business cases, and migration status in one view
- Doesn't migrate anything itself — aggregates/reports status from tools like Application Discovery Service, Migration Evaluator, and MGN
- **Migration Hub Orchestrator** adds pre-built, customizable workflow templates for complex, multi-step enterprise migrations (e.g., SAP, Microsoft SQL Server) — saves scripting each step by hand

> 🧠 **Exam Tip:** Think of Migration Hub as "mission control" across your whole migration — useful when a scenario needs visibility into migration progress across many servers/apps/accounts.

---

## 22. AWS Step Functions

- Build serverless **visual workflows (state machines)** to orchestrate multiple AWS services — not just Lambda, though that's the most common building block
- Workflow features: sequential steps, parallel branches, conditional (choice) logic, timeouts, retries, structured error handling — far more reliable than chaining functions with custom glue code
- Integrates with dozens of services: EC2, ECS/Fargate, DynamoDB, SNS, SQS, API Gateway, Glue, SageMaker, and even on-prem systems reachable via API
- Supports built-in **human approval steps** (waiting for a callback before continuing)
- Common use cases: order fulfillment pipelines, data processing/ETL pipelines, multi-step business processes needing visibility/retries/error handling

> 🧠 **Exam Tip:** "Orchestrate multiple steps/services with visual workflow, retries, and error handling" → **Step Functions**. Running a single, standalone piece of code in response to an event → just **Lambda**. Step Functions is for coordinating many steps together.

---

## 🎯 Key Exam Traps — Quick Recap

1. **STS** = temporary credentials (roles, federation, cross-account) — free, no IAM users needed
2. **IAM vs Cognito** — IAM = your AWS environment; Cognito = your app's end users
3. **Managed Microsoft AD / AD Connector** connect to on-prem AD; **Simple AD does not** (and is now closed to new customers)
4. **IAM Identity Center** = centralized login + permission sets across AWS accounts (formerly AWS SSO)
5. **WorkSpaces** (full virtual desktop) ≠ **AppStream 2.0** (single streamed app)
6. **IoT Core** = device ingestion layer (MQTT/HTTPS/LoRaWAN, Device Shadow)
7. **AppSync** (GraphQL, real-time sync) ≠ **API Gateway** (plain REST)
8. **Application Composer** generates CloudFormation — it doesn't replace it
9. **Device Farm** = testing on real devices, not building/deploying code
10. **AWS Backup** = centralized policy-driven backups across services, not manual snapshots
11. DR strategies ranked by cost/speed: **Backup & Restore < Pilot Light < Warm Standby < Multi-Site/Hot Site**
12. **DRS** (ongoing DR readiness) ≠ **MGN** (one-time lift-and-shift migration) — same tech, different purpose
13. **DataSync** = files/objects; **DMS** = databases; **DRS/MGN** = block-level server replication
14. **Application Discovery Service** (what do we have?) ≠ **Migration Evaluator** (business case/cost) ≠ **Migration Hub** (tracking dashboard)
15. **Step Functions** = orchestrating multiple steps/services; **Lambda** = a single function responding to one event
