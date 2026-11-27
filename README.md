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
