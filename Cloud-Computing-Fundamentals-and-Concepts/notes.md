# AWS Cloud Practitioner Study Notes

## Table of Contents

1. [5 Characteristics of Cloud Computing](#1-5-characteristics-of-cloud-computing)
2. [6 Advantages of Cloud Computing](#2-6-advantages-of-cloud-computing)
3. [AWS Pricing Models](#3-aws-pricing-models)
4. [Deployment Models of Cloud Computing](#4-deployment-models-of-cloud-computing)
5. [Types of Cloud Computing Models](#5-types-of-cloud-computing-models)
6. [AWS Regions](#6-aws-regions)
7. [AWS Availability Zones](#7-aws-availability-zones)
8. [Shared Responsibility Model](#8-shared-responsibility-model)
9. [AWS Cloud Adoption Framework (CAF)](#9-aws-cloud-adoption-framework-caf)
10. [Cloud Transformation Value Chain](#10-cloud-transformation-value-chain)
11. [AWS Professional Services and Partner Network](#11-aws-professional-services-and-partner-network)
12. [AWS re:Post](#12-aws-repost)
13. [AWS Managed Services (AMS)](#13-aws-managed-services-ams)

---

## 1. 5 Characteristics of Cloud Computing

These 5 characteristics come from the NIST definition of cloud computing. They are a favorite exam topic: you may be asked to identify which characteristic a scenario describes.

| # | Characteristic | Description | Example / Keyword |
|---|----------------|-------------|-------------------|
| 1 | On-demand self-service | Users can provision resources autonomously without requiring interaction with the service provider. | Launching an EC2 instance from the console in minutes, with no approval ticket or vendor call needed. Keyword: self-service |
| 2 | Broad network access | Resources are accessible over the network, from diverse client platforms (laptop, phone, tablet) using standard protocols. | Accessing resources from any device over the internet. |
| 3 | Multi-tenancy and resource pooling | Multiple customers share the same underlying physical infrastructure securely and in isolation from one another. This lets AWS optimize resource utilization and pass on cost savings. | Many customers securely sharing the same physical hardware. |
| 4 | Rapid elasticity and scalability | Resources can be swiftly acquired or released based on demand, enabling quick scaling up or down (and even out and in) as needed. | Auto Scaling adding EC2 instances during a traffic spike, then removing them once traffic drops. Keyword: auto scaling |
| 5 | Measured service | Usage is metered automatically, and customers are billed accurately based on actual consumption. This is the technical foundation behind AWS's pay-as-you-go pricing. | Pay only for what you use. Keyword: pay only for what you use |

**Exam Tip:** If a question describes a scenario and asks "which characteristic of cloud computing does this represent?", match the keyword:

| Keyword in scenario | Characteristic |
|---------------------|----------------|
| Self-service | On-demand self-service |
| Auto scaling | Rapid elasticity |
| Pay only for what you use | Measured service |

---

## 2. 6 Advantages of Cloud Computing

These are AWS's own "6 Advantages of Cloud Computing." They show up almost word-for-word as exam answer choices, so know all six by name.

| # | Advantage | Explanation |
|---|-----------|-------------|
| 1 | Trade capital expense (CAPEX) for variable expense (OPEX) | Shift from buying and owning data centers and hardware upfront to paying only for the compute you actually use. Lowers Total Cost of Ownership (TCO) since there is no large capital outlay before you even start. |
| 2 | Benefit from massive economies of scale | Because AWS aggregates usage across millions of customers, it achieves greater economies of scale, which translates into lower pay-as-you-go prices for everyone. |
| 3 | Stop guessing capacity | Scale resources based on actual measured demand instead of guessing capacity needs upfront. No more over-provisioning "just in case" or under-provisioning and hitting bottlenecks. |
| 4 | Increase speed and agility | New IT resources are a click away, cutting the time it takes to make resources available to developers from weeks to minutes. |
| 5 | Stop spending money running and maintaining data centers | Focus your team's time on projects that differentiate your business, not on racking servers or replacing failed drives. |
| 6 | Go global in minutes | Deploy your application in multiple AWS Regions around the world with a few clicks, giving customers lower latency at minimal extra cost. |

**Practical example:** A startup launching a mobile app does not lease data centers in Singapore, Frankfurt, and Virginia to serve users on three continents. They deploy the same application to those AWS Regions in minutes. That is "go global in minutes" in action.

---

## 3. AWS Pricing Models

| # | Pricing Model | Description | Details / Example |
|---|---------------|-------------|-------------------|
| 1 | Pay as you go | Pay only for the resources you actually use, without the need for significant upfront investments in hardware or infrastructure. | On-Demand EC2 instances billed per second, with no contract. |
| 2 | Save when you reserve | Minimize risk, predictably manage budgets, and commit to long-term usage in exchange for significant discounts (up to about 72%) over On-Demand pricing. | See the table below for supported services. |
| 3 | Pay less by using more | Volume-based discounts: the more you consume, the lower your per-unit price becomes through tiered pricing. | Data transferred out, or S3 storage stored. |
| 4 | Pay less as AWS grows | As AWS's scale and efficiency improve, AWS has historically passed savings on to customers through price reductions over time. | AWS has lowered prices many times since 2006. |

### Reservation options by service ("Save when you reserve")

| Service | Reservation Option |
|---------|--------------------|
| EC2 | Reserved Instances or Savings Plans |
| DynamoDB | Reserved Capacity |
| ElastiCache | Reserved Nodes |
| RDS | Reserved Instances |
| Redshift | Reserved Nodes |

**Exam tip:** Reserved Instances suit steady, predictable workloads (for example, a database that runs 24/7 all year), not spiky or short-lived workloads.

**Practical example:** A gaming company running a batch-rendering job that can be interrupted and resumed might use Spot Instances (not listed above, but tested on the exam) for up to 90% off On-Demand, while their always-on login servers use Reserved Instances or Savings Plans for predictable savings.

---

## 4. Deployment Models of Cloud Computing

| Model | Definition | Key Points | Example |
|-------|------------|------------|---------|
| Private Cloud | Cloud-like infrastructure dedicated exclusively to a single organization, whether hosted on-premises or by a third party. | Complete control over infrastructure and resources. Enhanced security and isolation, often chosen for highly regulated workloads. Tailored to meet specific business requirements and compliance needs. | A bank keeping its core transaction-processing systems in a dedicated private environment due to regulatory requirements. |
| Public Cloud | Cloud resources owned and operated by a third-party cloud provider (like AWS) and made available to anyone over the Internet. | No hardware to buy or maintain. You share physical infrastructure with other customers, but your data and resources stay logically isolated. | A startup running its entire web application on AWS with no on-premises servers at all. |
| Hybrid Cloud | Combines on-premises (or private) infrastructure with public cloud capabilities, allowing organizations to leverage both environments. | Provides control over sensitive assets by keeping them within the private infrastructure. Offers the flexibility and cost-effectiveness of the public cloud for scalable, less-sensitive workloads. | A hospital keeps patient records on-premises for compliance but bursts overflow compute for research and analytics into AWS. |

**Exam Tip:** Do not confuse deployment models (Private / Public / Hybrid: where the infrastructure lives) with service models (IaaS / PaaS / SaaS: how much AWS manages for you). The exam tests both, often in the same question set.

---

## 5. Types of Cloud Computing Models

| Model | Description | What You Manage | Examples |
|-------|-------------|-----------------|----------|
| Infrastructure as a Service (IaaS) | Offers virtualized computing resources (servers, storage, networking) over the internet. Maximum flexibility and control. | OS, middleware, and app. You are responsible for patching and scaling the OS layer. | Amazon EC2, Azure Virtual Machines, Google Compute Engine |
| Platform as a Service (PaaS) | Cloud platform for developing, deploying, and managing applications. AWS manages the OS and runtime for you. | Your code and data, not the underlying infrastructure. | AWS Elastic Beanstalk, AWS App Runner, Azure App Service |
| Software as a Service (SaaS) | A complete, ready-to-use application run and managed entirely by the provider. | Nothing on the infrastructure or platform side. You just log in and use it. | Gmail, Microsoft 365, Salesforce, Amazon Chime |

**Practical example (pizza-as-a-service):**

| Model | Analogy |
|-------|---------|
| IaaS | You buy ingredients and use your own kitchen. |
| PaaS | A pizza kit is delivered and you just bake it. |
| SaaS | A pizza arrives at your door, ready to eat. |

**Exam Tip:** As you move IaaS, then PaaS, then SaaS, AWS manages more of the stack and you manage less. Expect a "who is responsible for X" style question.

---

## 6. AWS Regions

- A Region is a cluster of data centers in a specific geographic area (for example, Northern Virginia, Frankfurt, Singapore).
- AWS currently has 39+ Regions worldwide, and the count grows every year.
- Region codes look like `us-east-1`, `eu-west-3`, `ap-southeast-2`.
- Most AWS services are region-scoped: resources you create in one Region (for example, an EC2 instance in `eu-west-3`) are not automatically visible in another Region.
- A handful of services are global (for example, IAM, Route 53, CloudFront).

### Factors for choosing an AWS Region

| # | Factor | Explanation |
|---|--------|-------------|
| 1 | Compliance | Data governance is ensured; data stays within the Region unless explicitly permitted. |
| 2 | Proximity | Being close to customers minimizes latency. |
| 3 | Service availability | Varies by Region; not all services are available in every Region. |
| 4 | Pricing | Varies by Region and is transparent on the service pricing page. |

**Exam Tip:** "Region-scoped vs. global service" is a classic exam trap. If a question describes a resource that "does not show up" after switching Regions in the console, the answer is almost always that the resource is region-scoped and you are looking in the wrong Region.

---

## 7. AWS Availability Zones

| Property | Detail |
|----------|--------|
| Count per Region | A minimum of 3, with several Regions having more (up to 6). |
| Naming example | `ap-southeast-2a`, `ap-southeast-2b`, `ap-southeast-2c` |
| Composition | Each AZ comprises one or more discrete data centers with redundant power, networking, and connectivity. |
| Physical separation | Physically separated (often tens of kilometers apart) from other AZs in the same Region. |
| Isolation | A disaster (fire, flood, power outage) affecting one AZ does not take down the others. |
| Connectivity | Connected by high-bandwidth, low-latency, redundant networking for fast, synchronous replication between AZs. |

**Exam Tip:** "Deploy across multiple Availability Zones for high availability" is one of the most repeated correct answers on the exam. If a scenario asks how to make an application resilient to a single data center failure, multi-AZ deployment is almost always part of the answer.

---

## 8. Shared Responsibility Model

The Shared Responsibility Model describes how security and compliance responsibilities are divided between AWS and the customer.

| Party | Responsible For | Includes |
|-------|-----------------|----------|
| AWS | "Security of the Cloud" | Protecting the infrastructure that runs all AWS services: hardware, software, networking, and the physical facilities that house them. Physical security, environmental controls (power, cooling, fire suppression), and patching the underlying host infrastructure. |
| Customer | "Security in the Cloud" | Securing whatever they put into the cloud and configure themselves. Data encryption, IAM permissions, security group/firewall rules, guest OS patching (on EC2), and application-level security. |

**Practical example (EC2):**

| Responsibility | Owner |
|----------------|-------|
| Physical server, hypervisor, and data center | AWS |
| Patching the guest operating system | Customer |
| Configuring the security group correctly | Customer |
| Not leaving database passwords in plaintext code | Customer |

The split shifts depending on the service. For a fully managed service like S3, AWS handles more (durability, replication), while you are still responsible for bucket policies and encryption settings.

**Exam Tip:** This is one of the highest-yield topics on the exam. Memorize the rule: AWS secures "of" the cloud (infrastructure); the customer secures "in" the cloud (their data and configuration). If a scenario asks "who is responsible for X," map X to physical/infrastructure (AWS) or data/config/access (customer).

**Collaboration:** Both AWS and customers must work together to ensure a secure and compliant cloud environment.

**Note:** Every managed AWS service has its own specific shared responsibility split. For example, RDS shifts more responsibility to AWS than a self-managed database on EC2 does. Keep the general "of/in" rule as your anchor point.

---

## 9. AWS Cloud Adoption Framework (CAF)

- The AWS Cloud Adoption Framework (AWS CAF) is AWS's structured guidance for helping organizations plan and execute cloud-driven digital transformation.
- Developed by AWS professionals, it incorporates AWS best practices and insights from thousands of customer engagements.
- AWS CAF identifies key organizational capabilities essential for successful cloud transformations, currently mapping roughly 47 capabilities.
- It organizes these capabilities into six perspectives: Business, People, Governance, Platform, Security, and Operations.

### The six perspectives

| # | Perspective | Focus | Common Stakeholders |
|---|-------------|-------|---------------------|
| 1 | Business | Aligning cloud investments with digital transformation goals and desired business outcomes. | CEO, CFO, COO, CIO, CTO |
| 2 | People | Fostering a culture of continuous growth and learning, emphasizing that change is a normal and integral aspect of business operations. | CIO, COO, CTO, Cloud director, Cross-functional and enterprise-wide leaders |
| 3 | Governance | Orchestrating cloud initiatives, maximizing organizational benefits and minimizing transformation-related risks. | Chief Transformation Officer, CIO, CTO, CFO, Chief Data Officer (CDO), Chief Risk Officer (CRO) |
| 4 | Platform | Building an enterprise-grade, scalable, hybrid cloud platform; modernizing existing workloads; supporting new cloud-native solutions. | CTO, Technology leaders, Architects, Engineers |
| 5 | Security | Achieving the confidentiality, integrity, and availability of your data and cloud workloads. | Chief Information Security Officer (CISO), Chief Compliance Officer (CCO), Internal audit leaders, Security architects and engineers |
| 6 | Operations | Ensuring that your cloud services are delivered at a level that meets the needs of your business. | Infrastructure and operations leaders, Site Reliability Engineers, IT service managers, CISO |

**Exam Tip:** The Cloud Practitioner exam typically only expects you to recognize the six perspectives by name and match a stakeholder (for example, CFO, CISO) to the right one. You will not need the 47 underlying capabilities memorized.

---

## 10. Cloud Transformation Value Chain

This value chain has two parts: four transformation domains (what changes) and four transformation phases (when it happens). They are easy to mix up, so keep them in separate mental buckets.

### The 4 Transformation Domains (what changes)

| Domain | Description |
|--------|-------------|
| Technology | Migrate and modernize legacy infrastructure, applications, and data/analytics platforms. |
| Process | Digitize, automate, and optimize business operations, for example using ML to improve fraud detection or customer service. |
| Organization | Reimagine the operating model, organizing teams around products and value streams using agile methods. |
| Product | Reimagine the business model by creating new value propositions and revenue models. |

### The 4 Transformation Phases (when it happens)

| Order | Phase | Description |
|-------|-------|-------------|
| 1 | Envision | Show how the cloud will accelerate business outcomes by identifying transformation opportunities and laying the groundwork for change. |
| 2 | Align | Identify capability gaps across the 6 AWS CAF Perspectives, resulting in an Action Plan. |
| 3 | Launch | Build and deliver pilot initiatives in production and demonstrate incremental business value. |
| 4 | Scale | Scale up pilot initiatives to achieve the desired business benefits while maintaining scope and impact. |

**Practical example:** A retailer moving to AWS might use the Technology domain to migrate its inventory database to RDS and the Process domain to automate reordering with Lambda, all while progressing through the phases:

| Phase | Retailer Action |
|-------|-----------------|
| Envision | Identify the business case |
| Align | Assess readiness across CAF perspectives |
| Launch | Pilot the new system in one store |
| Scale | Roll out to all stores |

**Exam Tip:** Remember the three groupings:

| Grouping | Question It Answers | Members |
|----------|---------------------|---------|
| Perspectives | Who is involved | Business, People, Governance, Platform, Security, Operations |
| Domains | What is transforming | Technology, Process, Organization, Product |
| Phases | When it happens | Envision, Align, Launch, Scale |

The exam may test whether you can tell these three groupings apart.

### Related updates

| Older Name / Resource | Current Answer | Note |
|-----------------------|----------------|------|
| AWS Landing Zone | AWS Control Tower | The modern solution for setting up a secure, governed, multi-account AWS environment with automated guardrails. It replaced the older, manually deployed AWS Landing Zone solution. |
| Quick Starts | AWS Solutions Library | If you see "Quick Starts" in older study material, know it is now the AWS Solutions Library. |

---

## 11. AWS Professional Services and Partner Network

- The AWS Professional Services organization is a global team of experts who collaborate with your team and a selected member of the AWS Partner Network (APN).

### APN structure

| Component | Description |
|-----------|-------------|
| APN Technology Partners | Providers of hardware, connectivity, and software solutions. |
| APN Consulting Partners | Professional services firms that assist with building and deploying applications on AWS. |
| APN Training Partners | Organizations that offer training and learning resources for AWS. |

### Partner programs

| Program | Purpose |
|---------|---------|
| AWS Competency Program | Recognizes APN Partners who have demonstrated technical proficiency and proven customer success in specialized solution areas. |
| AWS Navigate Program | Helps APN Partners enhance their skills and expertise as AWS Partners. |

---

## 12. AWS re:Post

- AWS re:Post is an AWS-managed Q&A service offering crowd-sourced, expert-reviewed answers to technical questions about AWS. It replaced the original AWS Forums.
- It is part of the AWS Free Tier.
- Community members can earn reputation points and build their expert status by providing accepted answers and reviewing answers from other users.
- Questions from AWS Premium Support customers that do not receive a community response are escalated to AWS Support engineers.
- AWS re:Post is not intended for time-sensitive or proprietary information questions.

| Component | Description |
|-----------|-------------|
| AWS re:Post | Community Q&A service with crowd-sourced, expert-reviewed answers. |
| AWS re:Post Knowledge Center | Contains the most frequent and common questions and requests. |

---

## 13. AWS Managed Services (AMS)

AWS Managed Services (AMS) provides infrastructure and application support on AWS.

| Aspect | Detail |
|--------|--------|
| What it is | A team of AWS experts who manage and operate your infrastructure for security, reliability, and availability. |
| Benefit | Helps organizations offload routine management tasks and focus on their business objectives. |
| Management model | Fully managed service, with AWS handling common activities such as change requests, monitoring, patch management, security, and backup services. |
| Operational goal | Implements best practices and maintains your AWS infrastructure to reduce operational overhead and risk. |
| Support hours | 24/7/365, ensuring continuous support. |

---

## Quick Revision Cheat Sheet

| Topic | Key Takeaway |
|-------|--------------|
| Cloud characteristics | Self-service, broad network access, resource pooling, rapid elasticity, measured service |
| Cloud advantages | CAPEX to OPEX, economies of scale, stop guessing capacity, speed and agility, stop running data centers, go global in minutes |
| Pricing models | Pay as you go, save when you reserve, pay less by using more, pay less as AWS grows |
| Deployment models | Private, Public, Hybrid (where infrastructure lives) |
| Service models | IaaS, PaaS, SaaS (how much AWS manages) |
| Region selection | Compliance, proximity, service availability, pricing |
| High availability | Deploy across multiple Availability Zones |
| Shared responsibility | AWS secures "of" the cloud; customer secures "in" the cloud |
| CAF perspectives | Business, People, Governance, Platform, Security, Operations |
| Transformation domains | Technology, Process, Organization, Product |
| Transformation phases | Envision, Align, Launch, Scale |
| Multi-account governance | AWS Control Tower |
| Community Q&A | AWS re:Post |
| Fully managed operations | AWS Managed Services (AMS) |
