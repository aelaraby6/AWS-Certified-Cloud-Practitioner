# 🖥️ Amazon EC2 — ملخص الامتحان (CCP)

> ملخص شامل لكل حاجة تخص EC2 عشان الامتحان، منظم في جداول وأقسام، وتحت كل جزء شرح بالعامية المصرية.

---

## 1️⃣ إيه هو EC2؟

**Amazon EC2 (Elastic Compute Cloud)** = a **virtual server (instance)** running in the AWS Cloud. The instance type determines CPU, memory, storage, and network capacity.

- Pay by the **second** (Linux) or **hour**, instead of buying physical servers.
- EC2 = the textbook example of **IaaS (Infrastructure as a Service)**: AWS manages hardware + virtualization, **you** manage OS + runtime + app.

> 💡 **Exam tip:** لو السؤال قال "full control over OS, AWS manages hardware" → الإجابة **EC2 / IaaS** مش Lambda ولا RDS.

### 🗣️ بالعامية:
EC2 يعني إنك بتأجر "كمبيوتر افتراضي" شغال جوه سيرفرات أمازون. إنت اللي بتتحكم في نظام التشغيل والبرامج، وأمازون هي اللي مسؤولة عن الهاردوير (السيرفر الفيزيائي) والفيرتشوالايزيشن. بتدفع بس على اللي بتستخدمه، مش لازم تشتري سيرفر وتصيانه بنفسك.

---

## 2️⃣ إمكانيات EC2

| القدرة | الوصف |
|---|---|
| Instance Types متنوعة | general-purpose, memory-intensive, GPU... إلخ |
| EBS | تخزين بيانات على virtual drives |
| ELB | توزيع الحمل (load) بين أكتر من instance |
| ASG (Auto Scaling Group) | Scaling تلقائي حسب الحمل |
| Flexibility | تكبير/تصغير القدرة الحسابية حسب الطلب |
| OS متعدد | يدعم أنظمة تشغيل وبرامج مختلفة |

> ⚠️ **Anti-pattern:** لو السؤال يوصف workload محتاج "zero server management" → **Lambda**. لو محتاج تخزين "objects/files" مش نظام تشغيل شغال → **S3**. لو السؤال ذكر "provisioning" أو "patching OS" أو "choosing instance type" → دي إشارة إنها **EC2**.

### 🗣️ بالعامية:
يعني EC2 مش بس سيرفر لوحده، هو جزء من منظومة كاملة: عندك تخزين (EBS)، وتوزيع حمل بين السيرفرات (ELB)، وسكيلينج تلقائي (ASG) لو الحمل زاد أو قل. المهم تفرق: لو حد بيقولك "مش عايز أدير سيرفرات خالص" ده مش EC2 ده Lambda، ولو بيقولك "عايز أخزن ملفات" ده S3.

---

## 3️⃣ User Data Script

- بيتنفذ عند **أول تشغيل (first boot)** بس — مش بيتكرر كل مرة تعمل reboot إلا لو ظبطته يدويًا.
- بيشتغل بصلاحيات **root/admin**.
- الاستخدامات: تثبيت تحديثات وبرامج، تنزيل ملفات، تشغيل أوامر إعداد (زي تشغيل web server).

**Bootstrapping** = تنفيذ أوامر عند بدء تشغيل الجهاز.

> ⚠️ **Common exam trap:** متلخبطش بين:
> - **User Data** → أوامر تتنفذ عند بدء التشغيل
> - **IAM Role** → صلاحيات للـ instance
> - **Instance Metadata** → معلومات عن الـ instance نفسه (بتتجاب من metadata service)

### 🗣️ بالعامية:
User Data زي "سكريبت بداية" بتحطه وقت ما تعمل create للـ instance، وبيتنفذ أول ما السيرفر يشتغل بس (مش هيتكرر تاني). ده بيفيدك مثلاً لو عايز السيرفر يظبط نفسه أوتوماتيك ويحمّل برنامج أو يعمل صفحة ويب من غير ما تدخل تعمل حاجة يدوي.

---

## 4️⃣ EC2 Sizing & Configuration

لما تعمل configure لـ instance بتختار:

| العنصر | التفاصيل |
|---|---|
| **OS** | Linux / Windows / macOS |
| **CPU** | عدد الأنوية والقوة الحسابية |
| **RAM** | حجم الذاكرة |
| **Storage** | Network-attached (EBS, EFS) أو Hardware (Instance Store) |
| **Network Card** | سرعة الكارت + Public IP |
| **Firewall** | Security Group settings (inbound/outbound) |
| **Bootstrap** | User Data script |

### 🗣️ بالعامية:
ده زي ما تكون بتشتري كمبيوتر وبتختار مواصفاته: بروسيسور، رامات، تخزين، كارت شبكة، وحتى الفايروول (اللي بيحدد مين يقدر يوصل للسيرفر). كل ده بتختاره وقت إنشاء الـ instance.

---

## 5️⃣ EC2 Instance Types (العائلات)

### تسمية الـ Instance (مثال: `m6g.2xlarge`)

| الجزء | المعنى |
|---|---|
| `m` | Instance family (general purpose,...) |
| `6` | رقم الجيل (كل ما زاد = أحدث وأفضل price-performance) |
| `g` | صفة إضافية (g = Graviton/ARM, n = network optimized, d = local NVMe) |
| `2xlarge` | الحجم (nano → micro → ... → xlarge → 2xlarge → 4xlarge...) |

> 💡 مش هتتسأل عن كل حرف، بس لازم تعرف حرف العائلة وإن رقم الجيل الأعلى = أداء/سعر أحسن.

### العائلات الأساسية

| العائلة | الحروف | الاستخدام |
|---|---|---|
| **General Purpose** | M, T | ويب سيرفرات، code repos — توازن بين compute/memory/network |
| **Compute Optimized** | C | batch processing, media transcoding, HPC, ML, gaming servers |
| **Memory Optimized** | R, X | databases, in-memory caching, BI real-time analytics |
| **Accelerated Computing** | P, G, Inf/Trn | ML training/inference, graphics rendering, HPC |
| **Storage Optimized** | I, D, H1 | OLTP, NoSQL DBs, data warehousing, distributed file systems |

> 💡 **Exam tip:** لو السؤال ذكر "GPU" أو "machine learning" → **Accelerated Computing** مش Compute Optimized (اللي هو بس عن vCPU قوي مش hardware acceleration).

### 🗣️ بالعامية:
كل عائلة اتصممت لغرض معين:
- **General Purpose (M,T):** لو مش عارف تحدد، ده الافتراضي المتوازن، كويس لمواقع الويب العادية.
- **Compute Optimized (C):** لو شغلك محتاج معالجة قوية (زي تحويل فيديوهات أو حسابات علمية).
- **Memory Optimized (R,X):** لو شغلك محتاج رامات كتير (زي قواعد بيانات كبيرة أو كاش).
- **Accelerated (P,G,Inf):** لو محتاج جي بي يو زي الذكاء الاصطناعي والرندرة.
- **Storage Optimized (I,D):** لو محتاج قراءة/كتابة سريعة جدًا على تخزين محلي (زي قواعد بيانات ضخمة).

---

## 6️⃣ AWS Right Sizing

- مش دايمًا أقوى instance = أفضل اختيار، بسبب مرونة الكلاود (elasticity).
- ابدأ بـ instance صغير، السكيلنج لفوق سهل.
- لازم تعمل مراجعة مستمرة للـ instances (قبل الترحيل Migration وبعده باستمرار).

| الأداة | الوظيفة |
|---|---|
| **AWS Compute Optimizer** | الأداة الرئيسية — بيستخدم ML على بيانات CloudWatch عشان يوصي بأفضل إعدادات لـ EC2, EBS, Lambda, ASG |
| **CloudWatch** | يوفر الـ metrics الخام (CPU, Network) اللي القرار بيتبني عليها |
| **Cost Explorer** | توصيات rightsizing من ناحية التكلفة |
| **Trusted Advisor** | بيحدد الـ instances قليلة الاستخدام كجزء من فحوصات توفير التكلفة |

> 💡 **Exam tip:** "automated recommendations to resize EC2 based on utilization" → **AWS Compute Optimizer**.

### 🗣️ بالعامية:
Right Sizing يعني إنك تختار المقاس الصح مش الأكبر. متسرعش وتاخد أقوى سيرفر من الأول، ابدأ صغير وكبّر لو احتجت. أمازون عندها أدوات بتراقب استخدامك وتقولك "ده سيرفرك مش مستخدم كويس، صغّره" أو العكس.

---

## 7️⃣ EC2 Purchasing Options — مقارنة شاملة

| الخيار | الوصف | أعلى خصم | استخدام مثالي |
|---|---|---|---|
| **On-Demand** | ادفع حسب الاستخدام، بدون التزام | — | Workloads غير متوقعة، تطوير واختبار، مشاريع قصيرة |
| **Reserved Instances (RI)** | حجز instance معين لمدة 1 أو 3 سنين | حتى 72% | أنظمة ثابتة 24/7 (زي قاعدة بيانات) |
| **Savings Plans** | التزام بمبلغ إنفاق ($/hour) وليس instance محدد | حتى 72% | مرونة أكبر عبر EC2/Fargate/Lambda |
| **Spot Instances** | سعة فايضة بخصم كبير، ممكن تتقفل بإنذار دقيقتين | حتى 90% | Batch jobs, CI/CD, workloads تتحمل الانقطاع |
| **Dedicated Hosts** | سيرفر فيزيائي بالكامل ليك (تحكم في الـ sockets/cores) | حتى 70% (Reservation) | تراخيص BYOL محتاجة رؤية على الهارد وير |
| **Dedicated Instances** | هارد وير مخصص لحسابك بس (من غير تحكم في مكان الوضع) | — | متطلبات عزل فيزيائي تنظيمية |
| **Capacity Reservations** | حجز سعة في AZ معينة بدون التزام بسعر معين | — | ضمان توفر السعة لمنتج حرج |

### تفاصيل مهمة:

**Reserved Instances:**
- Standard RI: خصم أعلى (72%) بس مالكش تغيير خصائص الـ instance.
- Convertible RI: خصم أقل (66%) بس تقدر تغيّر العائلة/الـ OS.
- 3 طرق دفع: All Upfront (أعلى خصم) / Partial Upfront / No Upfront (أقل خصم بس أرخص من On-Demand).
- 💡 AWS دلوقتي بتفضّل **Savings Plans** على RI لمعظم الحالات لمرونتها.

**Savings Plans:**
- **Compute Savings Plans:** الأكثر مرونة — بيتطبق على أي EC2 family/size/OS/region + Fargate + Lambda.
- **EC2 Instance Savings Plans:** خصم أعلى بس مقفول على عائلة instance معينة في region محدد.

**Spot Instances:**
- مفيش "مزايدة" (bidding) دلوقتي — بس بتطلب Spot وتدفع السعر الحالي.
- ⚠️ **Anti-pattern:** ماتستخدمهاش أبدًا لقواعد بيانات حرجة أو workloads محتاجة SLA صارم — دي فخ كلاسيكي في الامتحان.

**Dedicated Hosts vs Dedicated Instances:**
- **Dedicated Host** = سيرفر فيزيائي محدد بالكامل ليك، تتحكم في الـ sockets/cores (لازم للتراخيص زي Oracle/Microsoft).
- **Dedicated Instance** = الهاردوير single-tenant لحسابك بس، لكن من غير تحكم في مكان الوضع، وأمازون ممكن تنقلها بعد Stop/Start.

### 🗣️ بالعامية:
- **On-Demand:** زي ما تدفع فاتورة الكهربا حسب استهلاكك، مفيش التزام.
- **Reserved Instances:** زي إنك تحجز شقة بالسنة وتاخد خصم، بس لازم تلتزم بنفس المواصفات.
- **Savings Plans:** زي RI بس أمرن، بتلتزم بمبلغ فلوس مش instance معين، فتقدر تغيّر نوع السيرفر وتفضل خصمك زي ما هو.
- **Spot:** زي إنك تشتري تذاكر بأسعار مخفضة جدًا، بس ممكن يسحبوها منك في أي وقت لو احتاجوا المكان (بإنذار دقيقتين بس).
- **Dedicated Host:** يعني سيرفر فيزيائي كامل ليك وحدك، وعارف بالظبط هو مكوّن إزاي (مهم جدًا لو عندك تراخيص برامج مرتبطة بعدد الأنوية).
- **Dedicated Instance:** الهاردوير بتاعك لوحدك (محدش غيرك بيستخدمه)، بس مش بتشوف تفاصيل مكانه أو تتحكم فيه زي الـ Host.
- **Capacity Reservation:** يعني تحجز "مكان مضمون" في منطقة معينة عشان متتفاجأش إن السعة خلصت وقت الحاجة (زي حجز مكان في مطعم).

---

## 8️⃣ إزاي تختار الـ Purchasing Option الصح؟

1. حدد متطلبات الـ workload (compute, memory, storage, network)
2. حلل خصائص الأداء
3. اعتبر التكلفة (On-Demand vs RI vs Savings Plans vs Spot)
4. راجع مدى ملائمة كل عائلة
5. قيّم الـ instance families واختبرها
6. راقب وحسّن باستمرار

### 📌 جدول سريع للمراجعة (مهم جدًا للامتحان)

| السيناريو | الإجابة |
|---|---|
| Workload غير متوقع، قصير المدى، مشروع جديد | **On-Demand** |
| قاعدة بيانات تعمل 24/7 لمدة 3 سنين | **Reserved Instance / Savings Plan** |
| إنفاق مرن عبر EC2 + Fargate + Lambda | **Compute Savings Plan** |
| Batch job / big-data يتحمل الانقطاع، التكلفة أهم حاجة | **Spot Instance** |
| لازم تستخدم تراخيص per-core موجودة | **Dedicated Host** |
| محتاج سعة مضمونة في AZ لإطلاق منتج بس مش عايز تلتزم بسعر | **On-Demand Capacity Reservation** |

### 🗣️ بالعامية:
ده أهم جدول في الملف كله بصراحة — دايمًا في الامتحان بيديك سيناريو ويقولك "إيه أنسب خيار؟". احفظ الترابط ده كويس:
- مش متوقع/قصير → On-Demand
- ثابت وطويل المدى → Reserved/Savings Plan
- يتحمل الانقطاع وعايز أرخص سعر → Spot
- عندك تراخيص برامج → Dedicated Host
- عايز تضمن السعة بس من غير التزام سعري → Capacity Reservation

---

## ✅ خلاصة سريعة للمذاكرة قبل الامتحان
- EC2 = IaaS، إنت بتتحكم في OS، أمازون في الهاردوير.
- User Data يشتغل مرة واحدة بس عند أول تشغيل، وبصلاحيات root.
- اعرف الفرق بين العائلات: General / Compute / Memory / Storage / Accelerated.
- Compute Optimizer = أداة الـ Right Sizing الرسمية.
- ذاكر جدول الـ Purchasing Options كويس، لأنه بييجي في شكل سيناريوهات كتير.
- Spot = رخيص بس مش موثوق. Dedicated Host = غالي بس لازم للتراخيص.
