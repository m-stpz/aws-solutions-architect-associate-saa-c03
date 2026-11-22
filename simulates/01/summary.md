node aws-exam/scripts/attempt-server.mjs

Then open http://127.0.0.1:8787/. Opening the HTML directly still works, but downloads go to your browser's Downloads folder because a file:// page cannot write into project folders.

# to fix

- question 29 is repeated
- 46 is repeated

# todo

- [x] do test 1
- [x] review result test 1 + take notes
- [ ] study weakest areas
- [ ] do test 2
- [ ] review result test 1 + take notes
- [ ] study weakest areas
- [ ] https://skillbuilder.aws/category/exam-prep/solutions-architect-associate-SAA-C03
- [ ] https://www.youtube.com/watch?v=yh1PvLI3IGg

# to study

AWS SAA-C03 Study Focus: Weakest Areas (Test 1 Review)

## 1. Cost Optimization (Domain 4 — 70%)

- **S3 Storage Lens vs. AWS Trusted Advisor:** **S3 Storage Lens** provides organization-wide visibility, aggregated dashboards, and activity/cost-optimization metrics specifically for Amazon S3 across all accounts. **Trusted Advisor** runs broader account-level best-practice checks (like idle EC2 instances or unattached EBS volumes) rather than deep S3 usage analytics.
- **Fargate Spot vs. Fargate On-Demand:** Whenever a scenario asks to reduce container compute costs for **fault-tolerant, interruptible batch jobs** without managing EC2 hosts, choose **Fargate Spot** (up to 70% off On-Demand, with a 2-minute interruption notice). Use On-Demand Fargate only for critical, interruption-sensitive workloads.
- **NAT Instance vs. NAT Gateway:** **NAT Gateways** are the default for production because they are managed and highly available, but they carry fixed hourly (~$32/month) and per-GB processing fees. If a question explicitly specifies a **low-traffic dev/test VPC** where **high availability is not required** and asks for the _lowest cost_, a small **EC2 NAT Instance** is the correct answer.

---

## 2. Security, IAM & Governance (Domain 1 — 80%)

- **SCPs Never Grant Permissions (Cross-Account KMS):** Service Control Policies (SCPs) only define the _maximum_ permission boundary for an account; they **never grant permissions**. For cross-account decryption of SSE-KMS objects, you always need an explicit `Allow` in **both** the KMS Key Policy (Account A) and the caller's IAM Policy (Account B).
- **Preventive vs. Detective Guardrails (Permissions Boundaries):** **IAM Access Analyzer**, **AWS Config**, and **GuardDuty** are _detective_ (they alert or remediate _after_ an action occurs). To **prevent** privilege escalation when delegating role creation, enforce an **IAM Permissions Boundary** via a condition on `iam:CreateRole` and `iam:PutRolePermissionsBoundary`.
- **Compliance Mode vs. Governance Mode (WORM):** Both **S3 Object Lock** and **AWS Backup Vault Lock** offer two modes. **Governance mode** can be bypassed by users with special IAM permissions. Whenever a question states that _no one—not even administrators or the root user—can delete or shorten retention_, always pick **Compliance mode**.
- **Amazon Macie vs. Amazon Inspector:**
  - **Amazon Macie:** Scans **Amazon S3** objects using ML/pattern matching to discover and classify sensitive data like **PII** (names, credit cards, SSNs).
  - **Amazon Inspector:** Scans **EC2 instances, ECR container images, and Lambda functions** for software vulnerabilities (CVEs) and unintended network exposure.

---

## 3. Networking & Compute Placement

- **Stateless NACLs vs. Stateful Security Groups:** **Security Groups** are stateful (if inbound traffic is allowed, return traffic is automatically allowed). **Network ACLs (NACLs)** are **stateless**—every inbound and outbound rule is evaluated independently. If clients can reach port 443 but responses never arrive, you must explicitly allow outbound **ephemeral ports (`1024–65535`)** in the NACL.
- **EC2 Placement Groups:**
  - **Cluster:** Packs instances physically close together in a **single AZ** for the lowest network latency and highest throughput (ideal for tightly coupled **HPC**).
  - **Partition:** Spreads groups of instances across distinct hardware partitions (ideal for large distributed workloads like **Hadoop, Cassandra, or Kafka**).
  - **Spread:** Places individual instances on completely separate racks (max 7 per AZ) for small, critical workloads that must avoid correlated hardware failures.

---

## 4. Databases & Serverless Analytics

- **AWS Glue vs. Amazon Athena:** **AWS Glue** is for **data cataloging** (Crawlers) and serverless **Spark/Python ETL transformations**. **Amazon Athena** is strictly for running **ad-hoc serverless SQL queries** directly against data in S3 (pay per data scanned).
- **Aurora Global Database vs. RDS Cross-Region Read Replicas:** Standard **RDS Cross-Region Read Replicas** use asynchronous engine-level replication, which can lag by seconds or minutes. Whenever a relational database requires a **cross-Region RPO of less than 1 second** plus fast local reads in secondary Regions, choose **Amazon Aurora Global Database**.
