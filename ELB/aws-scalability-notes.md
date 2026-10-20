# AWS Scalability, High Availability, Load Balancers and Auto Scaling

Study notes for AWS Certified Cloud Practitioner (CLF-C02).

---

## 1. Scalability: An Introduction

Scalability is the ability of an application or system to handle increasing load by adapting.

| Type | Also Called | Description |
|------|-------------|-------------|
| Vertical Scalability | Scale up / down | Increase the size (resources) of a single instance |
| Horizontal Scalability | Scale out / in, elasticity | Increase the number of instances or nodes |

Scalability is closely related to, but distinct from, High Availability. A system can be scalable without being highly available, and vice versa.

### Call Center Analogy

| Concept | Call Center Example | Technical Equivalent |
|---------|---------------------|----------------------|
| Vertical Scalability | Upgrading one agent to handle more calls | Upgrading server hardware or instance size |
| Horizontal Scalability | Hiring more agents so work is distributed | Adding more instances behind a load balancer |
| High Availability | A second call center in another location, so calls are still answered if one site loses power | Running across multiple Availability Zones |

### Exam Tip: Keyword Mapping

| If the question says... | Think... |
|-------------------------|----------|
| Distribute load across multiple resources | Horizontal scaling |
| Upgrade to a bigger instance | Vertical scaling |
| Survive an entire data center or AZ failure | High Availability |

These three concepts are frequently mixed together in CLF-C02 scenario questions.

---

## 2. Vertical Scalability

- Involves increasing the size of the instance.
  - Example: moving an application from a `t2.micro` to a `t2.large`.
- Commonly used for non-distributed systems, such as databases.
- There is a limit, determined by hardware.

**Anti-pattern:** Relying only on vertical scaling for a production web application is a common exam wrong answer.

| Limitation | Detail |
|------------|--------|
| Hard ceiling | Hardware limits how large an instance can get |
| Downtime | Resizing usually requires stop, change instance type, start |
| No high availability | A single instance is still a single point of failure |

---

## 3. High Availability

- High availability often accompanies horizontal scaling.
- Application runs in at least two Availability Zones.
- Primary goal: keep the system operating despite a data center loss or disaster.
- Focus: minimizing downtime and ensuring continuous service availability.

### High Availability and Scalability for EC2

| Approach | Direction | What Changes | Tools / Notes |
|----------|-----------|--------------|---------------|
| Vertical Scaling | Scale up / down | Size of the instance | Example below |
| Horizontal Scaling | Scale out / in | Number of instances | Auto Scaling Groups and Load Balancers |
| High Availability | Multi-AZ | Placement of instances | Same application across multiple AZs, with ASG and Load Balancer spanning those AZs |

**Vertical scaling example range:**

| Instance | RAM | vCPUs |
|----------|-----|-------|
| t3.nano | 0.5 GB | 2 |
| u7in-24tb.224xlarge (memory-optimized High Memory) | 24 TiB | 896 |

Note: AWS's current-generation U7i family replaced the older U-1 (`u-*tb1`) instances, which are no longer available for new launches.

---

## 4. Scalability vs Elasticity vs Agility

| Term | Definition |
|------|------------|
| Scalability | Ability to accommodate a larger load by enhancing hardware (scale up) or adding nodes (scale out) |
| Elasticity | In a scalable system, automatic scaling based on load, enabling pay-per-use, demand matching and cost optimization |
| Agility | New IT resources can be provisioned quickly, reducing time to availability from weeks to minutes. Unrelated to scalability |

**Exam Tip:** Quick recap:

| Question | Answer |
|----------|--------|
| Can the system grow? | Scalability |
| Does it grow and shrink automatically with demand? | Elasticity |
| How fast can you get new resources in the first place? | Agility |

---

## 5. Load Balancers: An Introduction

Load balancers are servers that distribute incoming traffic across multiple downstream servers (such as EC2 instances) to balance the load.

### Why Use a Load Balancer

| Benefit | Description |
|---------|-------------|
| Load distribution | Spreads traffic across multiple downstream instances |
| Single point of access | Provides one DNS name for your application |
| Failure handling | Seamlessly handles failures of downstream instances |
| Health checks | Performs regular health checks on instances |
| SSL/TLS termination | Offloads HTTPS encryption work from your instances |
| High availability | Works across multiple zones |
| Session stickiness | Keeps a user on the same backend instance, when the application needs it |

**Exam Tip:** A load balancer is a regional resource that spans multiple Availability Zones, which is what makes it central to high availability. ELB itself does not scale your instances; that is the job of an Auto Scaling Group. They are almost always used together, but they solve different problems.

---

## 6. Elastic Load Balancer (ELB)

- ELB is a managed load balancer provided by AWS.
- AWS handles functionality, upgrades, maintenance and high availability.
- Configuration options are limited, but it reduces the effort and cost of running your own load balancer.

### The Four Types of ELB

| Type | OSI Layer | Protocols | Key Features | Typical Use Case |
|------|-----------|-----------|--------------|------------------|
| Application Load Balancer (ALB) | Layer 7 | HTTP, HTTPS, gRPC | Content-based routing by URL path, hostname, headers | Microservices, container-based applications |
| Network Load Balancer (NLB) | Layer 4 | TCP, UDP, TLS | Ultra-high performance, low latency, millions of requests per second, static IP / Elastic IP per AZ | Extreme performance, static IP needs |
| Gateway Load Balancer (GWLB) | Layer 3 | IP, using GENEVE on port 6081 | Deploys, scales and manages third-party virtual appliances transparently | Firewalls, intrusion detection/prevention systems |
| Classic Load Balancer (CLB) | Layer 4 and Layer 7 | TCP, SSL/TLS, HTTP, HTTPS | Legacy, lacks advanced routing rules | Existing workloads only; not recommended for new applications |

Note: CLB is different from "EC2-Classic" networking, which was fully retired in 2022-2023. The Classic Load Balancer product itself is still available.

### Exam Tip: Keyword Mapping

| If the question mentions... | Think... |
|-----------------------------|----------|
| Path-based or host-based routing, microservices, containers | ALB |
| Extreme performance, static IP, TCP/UDP | NLB |
| Third-party firewalls or appliances | Gateway Load Balancer |
| Legacy load balancer | CLB (legacy, but not shut down) |

---

## 7. Auto Scaling Groups (ASG): An Introduction

In real life, the load on websites and applications fluctuates. In the cloud, servers can be quickly provisioned or decommissioned.

### Goals of an ASG

| Goal | Description |
|------|-------------|
| Scale out | Add EC2 instances to handle increased load |
| Scale in | Remove EC2 instances to reduce excess capacity |
| Enforce limits | Maintain a specified minimum and maximum number of instances |
| Register with load balancer | Automatically register new instances |
| Self-healing | Replace unhealthy instances to ensure reliability |
| Cost optimization | Run only the necessary number of instances |

### ASG Definition

| Setting | Description | Required |
|---------|-------------|----------|
| Launch Template | Describes the instance: AMI, instance type, key pair, security groups, etc. | Yes |
| Minimum size | Lowest number of instances | Yes |
| Maximum size | Highest number of instances | Yes |
| Desired capacity | Target number of instances | Optional |

**Anti-pattern:** Using a Launch Configuration instead of a Launch Template is outdated.

| Feature | Launch Template | Launch Configuration |
|---------|-----------------|----------------------|
| Status | Current, recommended | Legacy |
| Mutability | Versioned | Immutable |
| Mixed instance types | Supported | Not supported |
| On-Demand plus Spot combination | Supported | Not supported |

**Exam Tip:** ASGs are free to use. You only pay for the underlying resources they launch (EC2 instances, EBS volumes, etc.).

---

## 8. ASG Scaling Strategies

| Strategy | Type | How It Works | Example |
|----------|------|--------------|---------|
| Manual Scaling | Manual | Adjust ASG size by hand as needed | Set desired capacity to 5 |
| Simple / Step Scaling | Dynamic | Add or remove a set number of instances when a CloudWatch alarm triggers | Add 2 if CPU > 70%, remove 1 if CPU < 30% |
| Target Tracking Scaling | Dynamic | Adjusts instance count to keep a metric near a target. Simplest to set up; recommended by AWS for most use cases | Keep average CPU around 40% |
| Scheduled Scaling | Scheduled | Scaling actions based on known usage patterns | Raise minimum capacity to 10 at 5 pm on Fridays |
| Predictive Scaling | Predictive | Uses machine learning on historical CloudWatch data to forecast traffic and provision instances in advance | Daily morning traffic surge |

**Predictive scaling note:** It works best combined with target tracking. Predictive scaling handles load you can forecast; target tracking reacts to anything unexpected on top of it.

### Warm Pools

| Aspect | Detail |
|--------|--------|
| What it is | A pool of pre-initialized (stopped or running) EC2 instances kept outside the ASG's active capacity |
| How it helps | On scale out, the ASG pulls ready instances from the warm pool instead of booting new ones from scratch |
| Benefit | Dramatically cuts scale-out time |
| Best for | Applications with long boot or initialization times |

---

## 9. Terminology Cheat Sheet

| Term | Meaning | Scaling Type |
|------|---------|--------------|
| Scale out | Add more instances | Horizontal |
| Scale in | Remove instances | Horizontal |
| Scale up | Increase instance size | Vertical |
| Scale down | Decrease instance size | Vertical |

**Exam Tip:** The exam is precise about this terminology. ASGs do scale out/in (horizontal), not scale up/down.

**Health check grace period:** An ASG's health check grace period matters. If it is too short, a slow-booting instance can be marked unhealthy and terminated before it finishes starting up.

---

## 10. Quick Review: Common Exam Traps

| Trap | Correct Understanding |
|------|-----------------------|
| Vertical scaling alone is enough for production web apps | It has a hard ceiling, requires downtime, and offers no high availability |
| ELB scales my instances | ELB distributes traffic; the Auto Scaling Group scales instances |
| Scalability and high availability are the same | They are related but distinct; one can exist without the other |
| Scalability, elasticity and agility are interchangeable | Grow vs automatic grow/shrink vs speed of provisioning |
| Launch Configurations are fine for new ASGs | Use Launch Templates |
| Classic Load Balancer has been shut down | It is legacy but still available |
| ASGs have an extra fee | ASGs are free; you pay for the resources they launch |
