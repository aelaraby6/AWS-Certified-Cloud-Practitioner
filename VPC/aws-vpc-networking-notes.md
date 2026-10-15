# AWS VPC Networking - Cloud Practitioner Study Notes

## 1. Introduction to VPC and Subnets

A VPC is your own private data center inside AWS. Nothing enters or leaves unless you explicitly allow it. The core idea the exam keeps testing is "deny by default, allow what you configure."

### Core Concepts

| Concept | Description | Scope |
|---|---|---|
| VPC (Virtual Private Cloud) | A logically isolated, private network you fully control within AWS | Regional resource (spans all AZs in the region) |
| Subnet | A slice of the VPC's IP range | Lives in exactly one Availability Zone |
| Route Table | Decides where a subnet's traffic goes (internet, another subnet, VPN, or nowhere) | One route table attached per subnet |
| Default VPC | Pre-configured VPC in each region of every account, with a public subnet in every AZ, an Internet Gateway attached, and auto-assigned public IPs | Per region |

The Default VPC is why a brand-new EC2 instance can reach the internet with zero networking setup.

### Public vs Private Subnets

| Feature | Public Subnet | Private Subnet |
|---|---|---|
| Route to Internet Gateway | Yes (`0.0.0.0/0` points to the IGW) | No direct route |
| Reachable from internet | Yes (if the resource also has a public IP) | No, isolated from inbound internet traffic |
| What makes it this type | Route table entry only | Absence of an IGW route |

**Exam anti-pattern:** A subnet is only "public" because of its route table entry pointing to an IGW. It is not public because of its CIDR range or its name. Renaming a subnet to "public-subnet" does nothing if the route table does not send `0.0.0.0/0` to an Internet Gateway.

---

## 2. IP Addresses in AWS

Public IPv4 pricing is testable on the Cloud Practitioner exam. AWS started charging for it in 2024.

| Address Type | Reachability | Persistence | Cost |
|---|---|---|---|
| Public IPv4 (auto-assigned) | Reachable from the internet | Changes every time the instance is stopped and started | $0.005/hour (about $3.65/month) |
| Private IPv4 | Only routable inside a private network (VPC, on-premises LAN, peered or connected network), e.g. 10.0.1.15, 192.168.1.1, 172.31.0.5 | Stays the same for the life of the instance, including stop/start | No charge |
| Elastic IP (EIP) | Static, account-owned public IPv4 address | Stays the same; attach and detach on demand | $0.005/hour (about $3.65/month), used or idle |
| IPv6 | Globally unique and public by design (no private IPv6 concept) | Stable | No additional charge for AWS-provided IPv6 |

### Key Points

- **Exam trap:** If you need a fixed public IP, you need an Elastic IP, not the default auto-assigned one.
- An Elastic IP lets you keep the same public IP even when you replace the underlying EC2 instance. This is useful for DR failover scenarios.
- AWS charges $0.005/hour for every public IPv4 address in use. This includes Elastic IPs, EC2 auto-assigned public IPs, and public IPs attached to a NAT Gateway or Load Balancer, whether the IP is actively used or idle.
- IPv6 has roughly 340 undecillion addresses (effectively unlimited). Example: `2001:db8:3333:4444:cccc:dddd:eeee:ffff`
- A VPC can be IPv4-only, dual-stack (IPv4 + IPv6), or IPv6-only for certain subnet types. The exam mainly wants you to know IPv6 exists and does not carry the public IPv4 hourly charge.

---

## 3. Internet Gateway and NAT Gateway

### Internet Gateway (IGW)

- A horizontally scaled, highly available VPC component that allows communication between instances in a VPC and the internet.
- One IGW per VPC.
- Free. You only pay for data transfer.
- A public subnet routes `0.0.0.0/0` to the IGW. Resources also need a public IP to be reachable, because the route alone is not enough.

### NAT Gateway vs NAT Instance

Both let private subnet resources initiate outbound internet connections (for example, downloading OS patches) without exposing them to inbound internet traffic.

| Feature | NAT Gateway | NAT Instance |
|---|---|---|
| Management | AWS-managed | Self-managed EC2 instance running NAT software |
| Availability | Highly available within a single AZ | You manage availability yourself |
| Scaling | Automatic | You manage scaling |
| Patching | None required | You patch it |
| Cost | Higher | Cheaper for very low, predictable traffic |
| Status | AWS recommended default | Largely legacy; new designs should default to NAT Gateway |

**High availability tip:** Deploy one NAT Gateway per AZ. A single NAT Gateway is an AZ-level single point of failure.

### IGW vs NAT Gateway

| Component | Purpose |
|---|---|
| Internet Gateway | Gives a subnet internet access (inbound and outbound) |
| NAT Gateway | Gives a private subnet outbound-only internet access |

**Common exam trap:** They solve different problems, and both may appear in the same VPC diagram.

---

## 4. Network ACLs and Security Groups

This is one of the highest-yield comparisons on the exam. The exam describes a symptom (for example, "return traffic is being blocked") and expects you to know whether NACLs or Security Groups are the cause. The stateless vs stateful difference is the key.

### Side-by-Side Comparison

| Feature | Security Group | Network ACL (NACL) |
|---|---|---|
| Level | Instance level (attached to an ENI) | Subnet level |
| Rule types | Only ALLOW rules (no explicit DENY; anything not allowed is implicitly denied) | ALLOW and DENY rules |
| State | Stateful: return traffic is automatically allowed regardless of any rules | Stateless: return traffic must be explicitly allowed by rules |
| Rule evaluation | All rules are evaluated before deciding whether to allow traffic | Rules are processed in number order (lowest first); first match wins |
| Rule sources | IP addresses/CIDR ranges and other security groups | IP addresses/CIDR ranges and ports only (no security group references) |
| Application | Applies to an instance only if someone specifies the security group at launch or associates it later | Applies to all instances in the subnets it is associated with; no reliance on users to specify it |
| Default behavior | Denies all inbound, allows all outbound by default | Default NACL allows all inbound and outbound; a custom NACL denies everything until rules are added |

### NACL Details

- Acts as a firewall controlling traffic in and out of an entire subnet.
- Supports both ALLOW and DENY rules.
- Applies to every resource in the subnet.
- Rules are evaluated by rule number, lowest first. The first matching rule wins.
- Rules are based on IP addresses/CIDR ranges and ports, not security group references.
- Stateless: if you allow inbound port 443, you must also explicitly allow the outbound ephemeral port range for the response to leave.
- The default NACL that ships with a VPC allows all traffic. A custom NACL you create denies everything by default until you add rules.

### Security Group Details

- A firewall that controls traffic to and from an Elastic Network Interface (ENI), typically attached to an EC2 instance, RDS instance, or Lambda function (in a VPC).
- Only ALLOW rules are supported.
- Rules can reference IP addresses/CIDR ranges and other security groups. Example: "allow port 3306 from the app-server security group." This is a favorite exam scenario for tiered architectures.
- Stateful: if inbound traffic is allowed, the matching outbound return traffic is automatically allowed, and vice versa. No need to configure both directions.
- An instance can have multiple security groups attached. Rules from all of them are combined (most permissive wins, since there is no DENY to conflict with).

---

## 5. Connectivity Options Overview

As you study connectivity, ask: "What am I connecting, and does traffic cross the public internet?" That question sorts almost every exam scenario into the right service.

| Service | Connects | Over Public Internet? | Encrypted? | Setup Time |
|---|---|---|---|---|
| VPC Peering | VPC to VPC | No (AWS internal network) | Private network | Fast |
| VPC Gateway Endpoint | VPC to S3 or DynamoDB | No | Private | Fast |
| VPC Interface Endpoint (PrivateLink) | VPC to most other AWS services | No | Private | Fast |
| AWS PrivateLink (Endpoint Service) | Your service to many consumer VPCs | No | Private | Fast |
| Site-to-Site VPN | On-premises network to VPC | Yes | Yes (IPsec) | Minutes |
| Direct Connect | On-premises network to AWS | No (dedicated physical line) | No (add a VPN for encryption) | Weeks to months |
| Client VPN | Individual user device to VPC and on-premises | Yes | Yes (TLS) | Fast |
| Transit Gateway | Many VPCs and on-premises networks | Depends on attachment | Depends on attachment | Fast |

---

## 6. VPC Peering

- Connects two VPCs privately over AWS's internal network, so resources in each VPC can talk using private IP addresses as if on the same network.
- Requires non-overlapping CIDR blocks. Overlapping IP ranges is the most common reason a peering connection cannot be created.
- Works across the same account or different accounts, and across the same region or different regions (inter-region peering).
- **Not transitive:** If VPC A is peered with VPC B, and VPC B is peered with VPC C, A cannot reach C through B. You must create a direct peering connection between every pair that needs to communicate.

### Peering Scaling Problem

| Number of VPCs (N) | Max Peering Connections N x (N-1) / 2 |
|---|---|
| 2 | 1 |
| 5 | 10 |
| 10 | 45 |
| 20 | 190 |

This is exactly the problem Transit Gateway solves.

---

## 7. VPC Endpoints

VPC Endpoints let resources inside your VPC reach AWS services privately, without going over the public internet, an Internet Gateway, or a NAT Gateway.

| Feature | Gateway Endpoint | Interface Endpoint |
|---|---|---|
| Supported services | Amazon S3 and DynamoDB only | Most other AWS services |
| Implementation | Route table entry | ENI with a private IP in your subnet (powered by AWS PrivateLink) |
| Cost | Free | Hourly charge + data processing charge |

**Exam tip (memorize word for word):** S3 and DynamoDB use a Gateway Endpoint; everything else uses an Interface Endpoint.

---

## 8. AWS PrivateLink (VPC Endpoint Services)

- The most secure and scalable way to expose your own service to thousands of other VPCs (including other AWS accounts) without VPC peering, Internet Gateways, NAT devices, or public route tables.
- Requires a Network Load Balancer (NLB) in the service provider's VPC.
- Creates an ENI (an Interface Endpoint) in the consumer's VPC.

### PrivateLink vs VPC Peering

| Feature | PrivateLink | VPC Peering |
|---|---|---|
| Direction | One-directional (consumer reaches a specific service, nothing more) | Two-way, full network connectivity |
| CIDR requirement | Overlapping CIDRs are fine (no full network merge) | Non-overlapping CIDRs required |
| Typical use | SaaS vendors exposing a service to many customer VPCs | Connecting two VPCs for full communication |
| Requires | NLB in provider VPC | Peering connection and route table entries |

---

## 9. Site-to-Site VPN

- Connects your on-premises network to a VPC over an encrypted tunnel (IPsec).
- Traffic still travels over the public internet. It is encrypted, but not on a dedicated private line (that is what Direct Connect is for).
- On-premises side: a Customer Gateway (CGW), a physical or software appliance you manage.
- AWS side: a Virtual Private Gateway (VGW) attached to your VPC, or a Transit Gateway for connecting many VPCs at once.
- Each connection provisions two tunnels for redundancy.
- Can go up quickly (typically minutes) since there is no physical circuit to install.
- Great for a fast or backup connection. Pair it with Direct Connect for best-practice resiliency.

---

## 10. Direct Connect

- A dedicated physical network connection between your on-premises data center and AWS, bypassing the public internet entirely.
- Consistent, low latency and higher throughput than an internet-based VPN, with lower data transfer rates at volume.
- Best for large, steady, or latency-sensitive workloads.
- Not encrypted by default. Layer a VPN on top if you need encryption.
- Setup takes weeks to months because it requires physical cross-connects at an AWS Direct Connect location.
- **Best practice:** Combine Direct Connect (primary) with Site-to-Site VPN (encrypted backup) for a resilient hybrid connection.

### Site-to-Site VPN vs Direct Connect

| Feature | Site-to-Site VPN | Direct Connect |
|---|---|---|
| Path | Public internet | Dedicated private physical line |
| Encryption | Yes (IPsec) by default | No by default |
| Setup time | Minutes | Weeks to months |
| Latency and throughput | Variable | Consistent, low latency, high throughput |
| Best for | Quick setup or backup connection | Large, steady, latency-sensitive workloads |

**Exam tip:** If a scenario needs connectivity quickly, the answer is Site-to-Site VPN. If it needs the most consistent, highest-throughput private connection and time is not the constraint, it is Direct Connect.

---

## 11. AWS Client VPN

- A managed, elastic, OpenVPN-based remote-access VPN.
- Lets an individual user connect from a laptop or device into your VPC and on-premises network through a single connection.
- Once connected, the user reaches EC2 instances and other resources over their private IP, as if their laptop were inside the VPC.
- TLS-encrypted and travels over the public internet.

### Client VPN vs Site-to-Site VPN

| Feature | Client VPN | Site-to-Site VPN |
|---|---|---|
| Connects | A single user's device to AWS | Network to network (whole office to AWS) |
| Scenario keyword | "A remote employee needs secure access" | "Our branch office needs a permanent connection" |
| Encryption | TLS | IPsec |
| Alternative for permanent connections | Not applicable | Direct Connect |

---

## 12. Transit Gateway

- Provides transitive routing between thousands of VPCs and on-premises networks through a single hub-and-spoke (star) architecture.
- Attach each VPC once to the Transit Gateway instead of peering every pair individually.
- A single Transit Gateway can attach up to 5,000 VPCs. It is the standard solution once an organization outgrows manual VPC peering.
- Integrates with Direct Connect Gateway and Site-to-Site VPN attachments, so on-premises networks join the same hub as your VPCs.

### Transit Gateway vs VPC Peering

| Feature | Transit Gateway | VPC Peering |
|---|---|---|
| Topology | Hub-and-spoke | Point-to-point |
| Transitive routing | Yes | No |
| Scale | Thousands of VPCs | Becomes unmanageable at scale |
| Billing | Per attachment-hour + data processed | No hourly attachment charge |
| Best for | Connecting many VPCs together, transitively, at scale | Connecting just two VPCs |

---

## 13. Monitoring the Network: VPC Flow Logs

VPC Flow Logs answer "who talked to whom, on what port, and was it allowed or rejected?" They do NOT capture packet contents, only metadata about the traffic. That distinction is a common exam trap.

### Scope Levels

| Level | What It Captures |
|---|---|
| VPC | Traffic for every ENI in the VPC |
| Subnet | Traffic for every ENI in that subnet |
| ENI | Traffic for one specific interface |

### Destinations

| Destination | Best For |
|---|---|
| Amazon S3 | Long-term storage and analysis (for example, with Athena) |
| CloudWatch Logs | Real-time monitoring and alerting |
| Kinesis Data Firehose | Streaming to further destinations such as a SIEM or third-party analytics tool |

### Use Cases

- Troubleshooting why traffic is not reaching an instance.
- Diagnosing overly restrictive Security Group or NACL rules (flow logs show `ACCEPT` vs `REJECT`).
- Feeding traffic data into security and anomaly-detection tooling.

### What Is and Is Not Captured

| Captured | Not Captured |
|---|---|
| Traffic between subnets | Packet payload (contents) |
| Traffic from the internet into a subnet | DNS queries (use Route 53 Resolver query logging instead) |
| Traffic out to the internet | Traffic to/from AWS-reserved addresses used by the DNS server, VPC router, or DHCP |
| Traffic on AWS-managed interfaces (Elastic Load Balancers, RDS, Aurora, ElastiCache) | |

**Exam anti-pattern:** Flow Logs are not real-time and can take several minutes to appear. Do not expect them to power second-by-second alerting on their own.

---

## 14. Quick Exam Cheat Sheet

| If the scenario says... | Answer |
|---|---|
| Need a fixed public IP | Elastic IP |
| Private subnet needs outbound-only internet | NAT Gateway |
| Subnet needs two-way internet access | Internet Gateway + route to `0.0.0.0/0` + public IP |
| Return traffic is being blocked | NACL (stateless) |
| Explicitly deny a specific IP address | NACL (Security Groups cannot deny) |
| Allow traffic only from another tier's resources | Security Group referencing another Security Group |
| Connect two VPCs | VPC Peering |
| Connect many VPCs transitively | Transit Gateway |
| Private access to S3 or DynamoDB | Gateway Endpoint |
| Private access to other AWS services | Interface Endpoint (PrivateLink) |
| Expose your service to many VPCs without IP planning | AWS PrivateLink (with NLB) |
| On-premises to AWS, fast setup | Site-to-Site VPN |
| On-premises to AWS, most consistent and highest throughput | Direct Connect |
| Remote employee needs secure access | Client VPN |
| Who talked to whom and was it accepted or rejected | VPC Flow Logs |
| Need packet contents or DNS queries | Not Flow Logs (use Route 53 Resolver query logging for DNS) |
