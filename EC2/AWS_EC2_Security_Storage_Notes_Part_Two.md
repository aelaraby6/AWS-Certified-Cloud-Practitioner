# Amazon EC2 — Security & Storage — Exam Notes

Summary of Security Groups, EBS, AMI, Instance Store, EFS, and FSx for the AWS CCP exam. Each section has the English material followed by an explanation in Egyptian Arabic.

---

## 1. Security Groups

**Material:**
- Security Groups are essential for network security in AWS. They manage inbound and outbound traffic for EC2 Instances.
- Security Groups consist solely of **rules**.
- Rules can specify permissions by **IP address** or by **referencing other Security Groups**.

**Explanation (Arabic):**
Security Group يعني مجموعة قواعد بتتحكم في مين يقدر يوصل للـ EC2 instance بتاعك (inbound) ومين هو يقدر يبعت له (outbound). القاعدة ممكن تحدد IP معين، أو تحدد Security Group تاني بحيث أي حاجة جواه يبقى مسموح لها تتواصل مع الـ instance بتاعك من غير ما تكتب IP لكل واحد لوحده.

---

## 2. Security Groups as a Firewall

**Material:**

| Property | Behavior |
|---|---|
| Scope | Attached to a specific Region + VPC combination (cannot cross VPCs) |
| Attachment | Can be attached to multiple instances |
| Location | Operates externally to the instance; blocked traffic never reaches it |
| State | Stateful |
| Default inbound | All inbound traffic blocked |
| Default outbound | All outbound traffic allowed |
| Rule type | Allow rules only — no explicit "deny" rule |

**Stateful behavior (diagram):**

```
Client  --- inbound request allowed ---->  EC2 Instance
Client  <-- response automatically allowed --  EC2 Instance
                 (no outbound rule needed)
```

- This is the key exam distinction from **Network ACLs (NACLs)**, which are **stateless** and require explicit rules for both directions.
- It's advisable to keep a **separate security group specifically for SSH/RDP access**.

**Troubleshooting table:**

| Symptom | Likely Cause |
|---|---|
| Connection times out / app not reachable | Security Group misconfiguration (traffic silently dropped) |
| "Connection refused" error | Security Group is probably fine — the application itself isn't running or isn't listening on that port |

**Useful ports (good to know, not tested directly):**

| Port | Protocol | Use |
|---|---|---|
| 22 | SSH | Log into a Linux instance |
| 22 | SFTP | Secure file upload/download (same port as SSH) |
| 21 | FTP | Unencrypted file upload — rarely used publicly today |
| 80 | HTTP | Unsecured website access |
| 443 | HTTPS | Secured (encrypted) website access |
| 3389 | RDP | Log into a Windows instance |

**Explanation (Arabic):**
فكر في الـ Security Group على إنه سور حوالين الـ instance بتاعك. أي حاجة داخلة (inbound) ممنوعة بشكل افتراضي، وأي حاجة خارجة (outbound) مسموحة بشكل افتراضي. المهم في الامتحان: الكلمة "stateful" يعني لو سمحت لطلب يدخل، الرد بتاعه هيخرج لوحده تلقائي من غير ما تحتاج تعمل قاعدة outbound خصيصًا له. ده عكس الـ NACL اللي هيتعرف قريب واللي فيه لازم تكتب قاعدة لكل اتجاه لوحده.

كمان مهم: الـ Security Group بيقدر بس يعمل "Allow"، مش بيقدر يعمل "Deny" صريح. لو عايز تمنع حاجة بشكل صريح فده بيبقى شغل الـ NACL مش الـ Security Group.

لو التطبيق بتاعك مش بيرد وحاصل timeout، غالبًا المشكلة في إعدادات الـ Security Group. لو ظهرلك "connection refused"، يبقى المشكلة مش في الـ Security Group غالبًا، لكن في التطبيق نفسه مش شغال أو مش بيسمع على البورت ده.

---

## 3. Elastic Block Store (EBS) Volumes

**Material:**
- An EBS Volume is a **network drive** that can be attached to instances while they run.
- Data persists independently of the EC2 instance's lifecycle (as long as the volume itself isn't deleted).
- At the CCP level: a standard EBS volume is attachable to **one instance at a time** (io1/io2 Multi-Attach is an advanced exception, not tested in depth).
- EBS volumes are **bound to a specific Availability Zone (AZ)**.
- Think of it as a "network USB stick."
- Because it's network-attached (not physical), there may be some latency.
- Can be detached from one instance and attached to another quickly — but only within the **same AZ**.
- `gp3` is now the recommended default General Purpose SSD volume type (over the older `gp2`) — it decouples IOPS/throughput from volume size, so performance can be provisioned independently of capacity.
- To move a volume across AZs, you must **create a snapshot first**.
- Volumes have a provisioned capacity, measured in **GBs and IOPS**. Billing is based on the **provisioned** capacity, regardless of actual usage. Capacity can be increased over time.

**Diagram — EBS is locked to an AZ:**

```
Region: us-east-1
┌───────────────────────────┐   ┌───────────────────────────┐
│  AZ: us-east-1a           │   │  AZ: us-east-1b           │
│  [EC2 Instance] --- [EBS] │   │  [EC2 Instance]            │
└───────────────────────────┘   └───────────────────────────┘
        cannot attach the EBS volume from 1a directly to an
        instance in 1b — must snapshot first, then restore in 1b
```

**Explanation (Arabic):**
EBS يعني قرص تخزين شغال عن طريق الشبكة (مش فيزيائي جوه السيرفر)، وممكن تفصله من instance وتوصله بـ instance تاني — بس بشرط الاتنين يكونوا في نفس الـ Availability Zone بالظبط. لو عايز تنقله لمنطقة (AZ) تانية، لازم الأول تعمل Snapshot، وبعدين تعمل استعادة (restore) منه في المكان الجديد.

الفوترة بتحصل على المساحة اللي إنت حجزتها (provisioned) مش على اللي إنت مستخدمه فعليًا، يعني لو حجزت 100 جيجا واستخدمت 10 بس، هتدفع على الـ 100.

نوع `gp3` هو الأحدث والموصى بيه دلوقتي بدل `gp2`، لأنه بيخليك تحدد الأداء (IOPS/throughput) لوحده منفصل عن حجم المساحة.

---

## 4. Delete on Termination Attribute

**Material:**
- Controls EBS behavior when an EC2 instance is terminated.
- By default: **root EBS volume is deleted** (attribute enabled).
- By default: **any other attached EBS volume is NOT deleted** (attribute disabled).
- Controllable via AWS Console / AWS CLI.
- Use case: preserve the root volume even after the instance is terminated.

**Explanation (Arabic):**
لما الـ instance يتقفل نهائيًا (Terminate)، القرص الأساسي (root volume) بيتحذف تلقائي، لكن أي قرص إضافي مضاف على الـ instance بيفضل موجود. تقدر تغيّر السلوك ده لو عايز تحافظ على القرص الأساسي حتى بعد إغلاق الـ instance.

---

## 5. EBS Snapshots

**Material:**
- A snapshot is a **backup of an EBS volume at a specific point in time**.
- Not necessary to detach the volume to take a snapshot, but it's recommended for consistency.
- Snapshots can be copied across **Availability Zones or Regions**.

**EBS Snapshot Archive:**
- Moves a snapshot to an "archive tier" for up to **75% lower storage cost** vs. standard tier.
- Best for snapshots rarely expected to be restored (e.g., end-of-project or compliance backups).
- Restoring from archive tier takes **24 to 72 hours**, incurs a per-GB retrieval fee, and has a **90-day minimum retention period**.

**Recycle Bin for EBS Snapshots:**
- Set rules to retain deleted snapshots so they can be recovered after accidental deletion.
- Retention period configurable from **1 day up to 1 year**.

**Explanation (Arabic):**
الـ Snapshot يعني صورة/نسخة احتياطية من الـ EBS في لحظة معينة. تقدر تنسخها لمنطقة تانية أو AZ تانية بسهولة، وده هو الطريقة الرسمية لنقل قرص من AZ لـ AZ تانية.

فيه خاصية اسمها Archive Tier، بتنقل الـ Snapshot لمكان أرخص جدًا (خصم يوصل 75%) بس الاستعادة منه بتاخد وقت طويل (من يوم لـ 3 أيام) ومفيها رسوم إضافية، ولازم تفضل محفوظة 90 يوم على الأقل.

وفيه كمان Recycle Bin يعني لو حد غلط وحذف Snapshot بالغلط، تقدر تسترجعه في فترة معينة إنت اللي بتحددها (من يوم لغاية سنة).

---

## 6. Amazon Machine Image (AMI)

**Material:**
- An AMI = a **pre-configured template** used to launch an EC2 instance.
- Think of it as a "snapshot" of a fully set-up server: OS, installed software, configuration, monitoring agents — all baked in.
- Speedier boot/configuration time — no need to reinstall everything via User Data every launch.
- An AMI is built for (and tied to) a specific Region, but can be **copied to other Regions**.

**Sources you can launch an EC2 instance from:**

| Source | Description |
|---|---|
| Public AMI | Provided and maintained by AWS (e.g., default Amazon Linux AMI) |
| Your own AMI | You build and maintain it, customized for your applications |
| AWS Marketplace AMI | Pre-built AMI created by a third party (may be sold, e.g. with a commercial security tool pre-installed) |

**How to build a custom AMI (workflow diagram):**

```
1. Launch & configure EC2 instance
            |
            v
2. Stop the instance
   (ensures data integrity/consistency)
            |
            v
3. Create AMI from the stopped instance
   (automatically creates underlying EBS snapshots)
            |
            v
4. Launch new instances from that AMI
   (identical, ready-to-go servers — e.g. in an Auto Scaling Group)
```

**Explanation (Arabic):**
الـ AMI يعني قالب جاهز لإنشاء instance جديد، ده بيشمل نظام التشغيل والبرامج والإعدادات كلها متظبطة من الأول، فبدل ما تعمل كل حاجة من الصفر بـ User Data كل مرة، تقدر تعمل instance من AMI جاهز وهيبقى مظبوط على طول.

الـ AMI مرتبط بـ Region معينة، لكن تقدر تنسخه لـ Region تانية لو عايز تشغل نفس السيرفر في مكان تاني.

عشان تعمل AMI بنفسك: تعمل instance وتظبطه، بعدين توقفه (Stop) عشان تضمن إن البيانات متسقة، بعدين تعمل AMI منه (وده هيعمل تلقائي EBS snapshots تحته)، وبعد كده تقدر تطلق instances جديدة من الـ AMI ده وهتيجي مطابقة تمامًا.

---

## 7. EC2 Image Builder

**Material:**
- Used to **automate the creation** of Virtual Machines or container images.
- Automates the creation, maintenance, validation, and testing of EC2 AMIs.
- Can be scheduled to run at specific intervals (e.g., weekly, or whenever packages are updated).
- It's a **free service** — you only pay for the underlying resources used.

**Explanation (Arabic):**
Image Builder يعني أداة بتخلي عملية عمل الـ AMI مؤتمتة بالكامل، يعني بدل ما تعمل الخطوات اللي فاتت يدويًا كل مرة، الأداة دي بتعملها لوحدها بشكل دوري (زي كل أسبوع مثلًا) وبتختبر الصورة كمان قبل ما تطلعها. الخدمة نفسها مجانية، بس بتدفع على الموارد اللي بتستخدمها وقت التشغيل.

---

## 8. EC2 Instance Store

**Material:**
- EBS volumes are network drives — good performance but ultimately network-limited.
- For maximum I/O performance, use **EC2 Instance Store**: physically-attached NVMe SSD storage on the underlying host, delivering much higher IOPS than any network-attached EBS volume.
- Instance Store volumes are **ephemeral**: data is lost if the instance is **stopped, hibernated, or terminated** (a simple reboot is safe).
- Suitable for buffer, cache, scratch data, or temporary content — **never** for data you can't afford to lose.
- Risk of data loss if underlying hardware fails; managing backups/replication is entirely your responsibility.

**Practical examples:** a Redis cache node, or a Hadoop/Spark worker node that recomputes data from a source of truth — losing the local disk is an inconvenience, not a disaster.

**Explanation (Arabic):**
Instance Store يعني قرص متصل فيزيائيًا بالسيرفر نفسه، مش عن طريق الشبكة زي EBS، فبيديك سرعة قراءة/كتابة أعلى بكتير. لكن المشكلة إنه "مؤقت" (ephemeral): لو الـ instance اتوقف أو اتقفل نهائيًا، البيانات كلها بتضيع (Reboot عادي بس آمن ومش بيمسح حاجة).

يعني ده مناسب بس للحاجات اللي مش مشكلة لو ضاعت، زي كاش مؤقت أو بيانات هتتحسب تاني من مصدر تاني، مش مناسب أبدًا لبيانات مهمة أو حساسة.

---

## 9. EFS — Elastic File System

**Material:**
- Managed **NFS (network file system)** that can be mounted concurrently on **hundreds/thousands** of EC2 instances.
- Works with **Linux** EC2 instances (not natively with Windows).
- Can be mounted from instances across **multiple Availability Zones in the same region simultaneously** — this is the key differentiator from EBS (which is locked to one AZ).
- Highly available and durable by design (data stored redundantly across multiple AZs).
- Scalable automatically, no capacity planning needed.
- Pay only for storage used, but noticeably more expensive per GB than EBS gp3.

**Explanation (Arabic):**
EFS يعني نظام ملفات مُدار (زي هارد ديسك مشترك) تقدر توصله بمئات أو آلاف الـ instances في نفس الوقت، وده الفرق الأساسي عن EBS اللي بيتوصل بـ instance واحد بس في AZ واحدة. EFS شغال بس مع Linux، ومش بيحتاج منك تخطيط للمساحة لأنه بيكبر لوحده تلقائي. السعر بالجيجا أغلى من EBS gp3 لكنه بيوفرلك مشاركة الملفات عبر مناطق (AZs) متعددة.

---

## 10. EFS Storage Classes — Infrequent Access & Archive

**Material:**

| Storage Class | Best For | Discount vs Standard |
|---|---|---|
| EFS Standard | Frequently accessed files | — |
| EFS Infrequent Access (EFS-IA) | Files accessed a few times a quarter | Up to 94% lower storage cost (small per-GB retrieval fee applies) |
| EFS Archive | Files accessed a few times a year | Further savings on top of IA pricing |

- **Lifecycle Policy**: EFS automatically moves files between classes based on last access time (e.g., move files not accessed for 30 days to IA, and files not accessed for 90 days to Archive).
- **EFS Intelligent-Tiering**: monitors access patterns and automatically moves files back to Standard if they become frequently accessed again — useful when access patterns are unpredictable.
- All tiering is **transparent to applications**: they keep reading/writing through the same file system namespace and mount point regardless of which storage class a file currently lives in.

**Explanation (Arabic):**
EFS-IA يعني تخزين أرخص للملفات اللي مش بتتفتح كتير (مرات قليلة في الربع سنة)، وفيه رسوم بسيطة كل مرة تفتح فيها الملف. EFS Archive أرخص كمان وده للملفات اللي بتتفتح مرات قليلة جدًا في السنة كلها. الحلو في الموضوع إن أمازون بتنقل الملفات دي لوحدها أوتوماتيك بناءً على قاعدة (Lifecycle Policy) إنت بتحددها، وبرضو فيه خاصية Intelligent-Tiering بترجع الملف لمكانه العادي (Standard) لو حسّت إنه بقى بيتفتح كتير تاني. كل ده بيحصل من غير ما التطبيق بتاعك يحس بأي فرق، بيكمل يقرأ ويكتب عادي.

---

## 11. Amazon FSx — Managed File Storage

**Material:**
- Amazon FSx launches and manages **third-party, purpose-built file systems** for specific workloads that EFS/EBS don't natively support.

| FSx Type | Description | Use Case |
|---|---|---|
| FSx for Windows File Server | Managed, Windows-native file system supporting SMB protocol and Active Directory integration | Lift-and-shift a Windows app expecting a native Windows file share |
| FSx for Lustre | High-performance file system for compute-intensive workloads | Machine learning, HPC; integrates directly with Amazon S3 |
| FSx for NetApp ONTAP / FSx for OpenZFS | Managed versions of these popular third-party file systems | Migrating existing NetApp or ZFS-based workloads to AWS without re-architecting |

**Explanation (Arabic):**
FSx يعني أمازون بتشغلّك وتديرلك نظام ملفات معروف من طرف تالت (زي Windows File Server أو Lustre أو NetApp أو ZFS)، من غير ما تحتاج تبني أو تصيّن السيستم ده بنفسك. يعني لو عندك تطبيق Windows قديم محتاج SMB Share، تستخدم FSx for Windows. لو عندك شغل ML أو HPC محتاج سرعة عالية ومتكامل مع S3، تستخدم FSx for Lustre. ولو عندك بيانات أصلًا شغالة على NetApp أو ZFS وعايز تنقلها لأمازون من غير ما تغيّر البنية، تستخدم النسخة المدارة المناسبة.

---

## Quick Comparison Table — EBS vs Instance Store vs EFS vs FSx

| Feature | EBS | Instance Store | EFS | FSx |
|---|---|---|---|---|
| Attachment | Network drive, one instance (standard) | Physically attached to host | Mounted by many instances at once | Mounted by many instances at once |
| AZ scope | Locked to one AZ | Locked to the host (one instance) | Multi-AZ within a region | Depends on deployment type |
| OS support | Linux/Windows | Linux/Windows | Linux (not native Windows) | Windows (native) / Linux depending on type |
| Persistence | Persists independently of instance | Ephemeral — lost on stop/terminate | Persists, highly durable | Persists, highly durable |
| Typical use | General-purpose block storage, boot volumes | Cache, buffer, scratch data | Shared file storage across many Linux instances | Specific third-party file system needs (Windows/Lustre/NetApp/ZFS) |

**Explanation (Arabic):**
الجدول ده بيلخص أهم فرق هيتسأل عنه في الامتحان: EBS مرتبط بمكان وinstance واحد. Instance Store أسرع لكنه بيضيع البيانات لو الـ instance اتقفل. EFS بيتشارك بين instances كتير في مناطق مختلفة لكن بس على Linux. FSx بتستخدمه لو محتاج نظام ملفات معين زي Windows أو Lustre أو NetApp.

---

## Summary — Key Points to Remember Before the Exam

- Security Groups: stateful, allow-only, default deny inbound / allow outbound, scoped to Region + VPC.
- NACLs (compare only): stateless, support allow AND deny, require rules both ways.
- EBS: network drive, locked to one AZ, snapshot needed to move across AZ/Region, billed on provisioned capacity.
- Delete on Termination: root volume deleted by default, other volumes kept by default.
- Snapshots: point-in-time backup, can move across AZ/Region, Archive tier for cold backups, Recycle Bin for accidental deletion protection.
- AMI: pre-configured launch template, region-specific but copyable, built from a stopped instance.
- Instance Store: fastest but ephemeral — never for critical data.
- EFS: shared Linux file storage across multiple AZs, has IA and Archive storage classes.
- FSx: managed third-party file systems (Windows, Lustre, NetApp, OpenZFS).

**Explanation (Arabic):**
لو مذاكرتش غير الجزء ده بس، خد بالك من: الفرق بين Security Group (stateful) والـ NACL (stateless)، الفرق بين EBS (مرتبط بـ AZ واحدة) و EFS (بيشتغل عبر AZs كتير) و Instance Store (سريع لكن مؤقت)، وإن AMI هو القالب الجاهز لعمل instances سريعة ومطابقة لبعض.
