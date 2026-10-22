# AWS Databases: Study Notes

## 1. Relational vs. Non-Relational Databases

| Aspect | Relational (SQL) | Non-Relational (NoSQL) |
|---|---|---|
| **Structure** | Tables with rows and columns | Key-value, document, graph, or wide-column |
| **Schema** | Fixed; defined up front | Dynamic (schema-less); items can differ |
| **Relationships** | Complex, via foreign keys and joins | Not enforced; optimized for simple, fast lookups |
| **Query language** | SQL | Varies by database type |
| **Best for** | Transactional systems needing strong consistency (OLTP): banking, orders, inventory | Massive horizontal scale, flexible data, very low latency: mobile apps, gaming leaderboards, IoT, real-time bidding |
| **AWS examples** | RDS, Aurora | DynamoDB (key-value), DocumentDB (document), Neptune (graph), ElastiCache (in-memory) |

> **Exam Tip:** Millions of requests/sec with unpredictable traffic → **DynamoDB**. Complex multi-table joins or strict relational integrity → **RDS / Aurora**.

---

## 2. Amazon RDS (Relational Database Service)

- Managed service for relational databases; AWS handles the "undifferentiated heavy lifting."
- **Supported engines (6):** PostgreSQL, MySQL, MariaDB, Oracle, Microsoft SQL Server, Db2.

> **Exam Tip:** **Aurora is NOT an RDS engine.** It is a separate AWS-built service, though often taught alongside RDS.

### RDS Deployment Options

| Option | Purpose | Key Points |
|---|---|---|
| **Read Replicas** | Scale reads | Up to 15 replicas; asynchronous replication; primary handles all writes |
| **Multi-AZ** | High availability | Automatic failover during AZ outage; primary handles all reads/writes; one standby AZ (a variant offers two readable standbys) |
| **Multi-Region (Read Replicas)** | Disaster recovery and global reads | Local read performance worldwide; replication cost |

### RDS vs. Self-Managed Database on EC2

| Task | RDS | EC2 (self-managed) |
|---|---|---|
| OS and engine patching | AWS | You |
| Backups (automated, point-in-time restore) | AWS | You |
| Failover / Multi-AZ | Built in | You |
| Monitoring | CloudWatch dashboards | You |
| Scaling | Vertical scaling, storage autoscaling, read replicas | You |
| OS-level (SSH) access | No | Yes |
| Unsupported engines | No | Yes |

- Storage is backed by Amazon EBS; maintenance windows are configurable.
- **Pick RDS** for less management overhead. **Pick EC2** when you need OS-level access or an unsupported engine.

---

## 3. Amazon Aurora

- AWS's cloud-native relational database, **compatible with MySQL and PostgreSQL** (existing code, drivers, and tools generally work unchanged).
- Built-in security, continuous backups, serverless option, up to **15 read replicas**, automated multi-Region replication.

| Feature | Detail |
|---|---|
| Performance (AWS claims) | Up to 5x MySQL and 3x PostgreSQL throughput vs. standard RDS (workload-dependent) |
| Storage | Auto-scales in 10 GB increments, up to 256 TiB per cluster |
| Pricing | Typically higher than equivalent RDS instances; varies by region and instance type |
| Free Tier | Aurora PostgreSQL Serverless available on the Free Tier (as of 2026) |

### Aurora Serverless
- Automatically scales capacity up and down based on demand (MySQL and PostgreSQL editions).
- Pay per second, measured in **Aurora Capacity Units (ACUs)**.
- Ideal for irregular, sporadic, or unpredictable workloads (dev/test, infrequent bursts).

---

## 4. NoSQL Basics

- **Advantages:** flexibility (easy data model evolution), horizontal scalability, high performance, purpose-built functionality.
- **Types:** key-value, document, graph, in-memory, search.
- **JSON:** the natural fit for NoSQL. It supports nested structures, fields that can be added or changed freely, and varied types (arrays, strings, numbers, booleans).

---

## 5. NoSQL and Caching Services

| Service | Type | Key Points |
|---|---|---|
| **DynamoDB** | Key-value / document | Serverless; single-digit-ms latency at any scale; On-Demand or Provisioned (with Auto Scaling) capacity; pay for requests and storage; use cases: shopping carts, leaderboards, session state, IoT |
| **DynamoDB Global Tables** | Multi-Region | Low latency in multiple Regions; active-active replication (read/write in any Region) |
| **DocumentDB** | Document (MongoDB-compatible) | Stores JSON-like documents; Aurora-like architecture; data replicated across 3 AZs; storage grows in 10 GB increments; millions of requests/sec |
| **Neptune** | Graph | Relationships matter as much as the data; 3 AZs; up to 15 read replicas; billions of relationships at millisecond latency; use cases: social networks, knowledge graphs, fraud detection, recommendations |
| **Timestream** | Time series | Serverless; trillions of events/day; automatic tiering (recent data in fast storage, older data in cheaper storage); built-in time-series analytics; use cases: sensors, metrics, telemetry |
| **ElastiCache** | In-memory cache | Managed Redis or Memcached; microsecond reads; absorbs repetitive reads to reduce database load |
| **DAX** | In-memory cache (DynamoDB only) | Up to 10x faster (ms → µs); compatible with DynamoDB API, so minimal code changes |

> **Exam Tips**
> - **MongoDB** mentioned → **DocumentDB**. General "NoSQL at massive scale" → **DynamoDB**.
> - Cache **specifically for DynamoDB** → **DAX**. General-purpose cache for any database → **ElastiCache**.

---

## 6. Analytics Services

| Service | What It Does | Key Points |
|---|---|---|
| **Redshift** | Data warehouse (OLAP) | PostgreSQL-based but **not for OLTP**; columnar storage + MPP; petabyte scale; batch loading then heavy querying; Multi-AZ; integrates with QuickSight and Tableau; pay per node, or capacity consumed with Serverless |
| **Redshift Serverless** | Auto-provisioned warehouse | No infrastructure management; pay only for what you use; reporting, dashboards, real-time analytics |
| **EMR** | Managed big data platform | Hadoop, Spark, HBase, Flink, Hive, Presto/Trino on EC2 clusters; ETL, data processing, ML data prep; scalable; Spot Instances for non-critical nodes |
| **Athena** | Serverless SQL on S3 | No loading needed; Presto/Trino-based; supports CSV, JSON, ORC, Avro, Parquet; **$5.00 per TB scanned**; use Parquet and partitioning to cut costs by up to 90%; common for ad hoc BI and log analysis (VPC Flow, ALB, CloudTrail) |
| **QuickSight** | Serverless ML-powered BI | Interactive dashboards; embeddable; per-session pricing; anomaly detection and forecasting; connects to RDS, Aurora, Athena, Redshift, S3, third-party sources |
| **Glue** | Serverless ETL | Discovers, prepares, and combines data; **Glue Data Catalog** is the central metadata repository used by Athena and Redshift Spectrum |

---

## 7. Ledger and Blockchain

| Service | Status | Details |
|---|---|---|
| **QLDB** | **Retired** (support ended July 31, 2025) | Was an immutable, cryptographically verifiable, append-only log for a single trusted owner (no decentralization). Migration path: **Aurora PostgreSQL** |
| **Managed Blockchain** | Active | Multi-party transactions without a central authority; join public networks or create private ones; supports **Hyperledger Fabric** |

---

## 8. Database Migration

### AWS DMS (Database Migration Service)
- Moves data into AWS quickly and securely; **the source stays operational** during migration (minimal downtime).
- Supports ongoing replication / **Change Data Capture (CDC)** to keep the target in sync until cutover.

| Migration Type | Description | Example | Schema Conversion Needed? |
|---|---|---|---|
| **Homogeneous** | Same engine on both sides | Oracle → Oracle | No |
| **Heterogeneous** | Different engines | SQL Server → Aurora | Yes |

### Schema Conversion Tools

| Tool | Type | Best For |
|---|---|---|
| **DMS Schema Conversion (DMS SC)** | Managed, web-based, built into the DMS console (no install) | AWS's recommended path for most conversions |
| **AWS SCT** | Standalone desktop app (separate install) | Certain conversion paths or features not covered by DMS SC; large data warehouse migrations (e.g., to Redshift) |

- Both convert what they can and flag objects needing manual rework (complex stored procedures, engine-specific features).
- **DMS (data) + Schema Conversion (schema) = a complete heterogeneous migration.**

---

## 9. Quick-Reference: Which Service?

| Scenario | Answer |
|---|---|
| Transactional app with joins and strict integrity | RDS / Aurora |
| Massive scale, low latency, unpredictable traffic | DynamoDB |
| MongoDB-compatible workload | DocumentDB |
| Highly connected data (social graph, fraud) | Neptune |
| Sensor / time-stamped data | Timestream |
| General-purpose in-memory cache | ElastiCache |
| Cache specifically for DynamoDB | DAX |
| Data warehouse / OLAP / petabyte analytics | Redshift |
| SQL directly on S3 data, no loading | Athena |
| Interactive BI dashboards | QuickSight |
| Big data processing with Hadoop/Spark | EMR |
| ETL and data catalog | Glue |
| Migrate databases into AWS with minimal downtime | DMS |
| Unpredictable / sporadic relational workload | Aurora Serverless |
| Least operational overhead | DynamoDB or Aurora Serverless |
| Who patches the RDS operating system? | **AWS** |
