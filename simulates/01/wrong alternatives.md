# Simulate attempt 1

9. A company stores millions of documents in Amazon S3 and needs to identify which buckets contain personally identifiable information (PII) such as names, addresses, and credit card numbers. The security team wants a managed service that discovers, classifies, and reports sensitive data without building custom scanning jobs. Which service should the solutions architect recommend?

- My answer:
  - Amazon inspector: wrong
    - why?
      - inspector assesses vulnerabilities in compute workloads and container images, no s3 data classification

- Correct:
  - Macie
    - uses managed ML and pattern matching to discover and classify sensitive data such as PII in s3, and generates findings for reviews

- Other alternatives
  - AWS config: evaluates resource config compliance, not the contents of s3 objects
  - Amazon GuardDuty: detects threats and anomalous behavior. It doesn't discover/classify PII in s3 objects

> Use Macie for managed sensitive-data discovery and PII classification in S3

11. A company wants organization-wide visibility into Amazon S3 storage usage, activity trends, and cost optimization opportunities across all of its accounts. Which S3 feature provides this visibility with aggregated dashboards and metrics?

- My answer:
  - Trusted advisor: wrong
    - provides best-practice checks, but it's not a detailed s3 usage and activity analytics service

- Correct:
  - Storage lens: provides organization-wide dashboards and metrics for storage usage, and cost optimization across accounts

- Other alternatives:
  - AWS CloudTrail data events: records API activity for auditing. It's not an S3 storage usage and cost analytics service
  - S3 inventory: provides object listing for a bucket, not organization-wise usage dashboards or cost recommendations

> Use s3 storage lens for organization-wise s3 usage, activity, and cost-optimization visibility

20. A public web application on EC2 instances was working. A security engineer then added a network ACL to the public subnet that allows inbound TCP 443 from the internet and outbound TCP 443 to the internet, but denies all other traffic. Clients can now establish TCP connections, but HTTPS responses never arrive. What is the most likely cause and fix?

- My answer:
  - The internet gateway is missing a route; add a route to 0.0.0.0/0: wrong
    - if the internet gateway route was missing, connections would not establish at all. The scenario says clients can establish TCP connections

- Correct:
  - The NACL is stateless; allow outbound TCP ports 1024-65535 to the internet so return traffic to clients can leave the subnet
    - NACL are stateless, so return traffic to client ephemeral ports must be explicitly allowed
    - security groups are stateful and don't require this rule

- Other alternatives
  - The security group is blocking return traffic; add an outbound rule for TCP 443 to 0.0.0.0/0: The security groups are stateful, so return traffic for an allowed inbound connection is automatically allowed. the NACL is the likely blocker
  - Disable source/destination checks on the EC2 instances: source/destination checks apply to NAP instances and appliances, not standard web server instances

> NACLs are stateless; allow ephemeral port returns, typically 1024-65535, in the outbound direction; security groups are steteful

25. A tightly coupled high-performance computing (HPC) workload runs on multiple EC2 instances in a single Availability Zone. The instances need the lowest possible network latency and highest throughput between each other. Which placement strategy meets these requirements?

- My answer:
  - A partition placement group
    - Partition placement groups isolate groups of instances across hardware for large distributed workloads. They don't provide the lowest inter-instance latency

- Correct:
  - A cluster placement group
    - Cluster placement group pack instances close together in a single AZ, providing low latency and high networkt throughput for tightly coupled HPC workloads

- Other alternatives:
  - No placement group with instances spread across multiple AZSs: adds network latency and not suitable for tightly coupled HPC workloads
  - Spread placement group: it spreads instances on separate hardware to reduce correlated failures, not minimize network latency

> Use a cluster placement group for tightly coupled HCP workloads that need low latency and high throughput in on AZ

27. A company runs fault-tolerant batch containers on AWS Fargate and wants to reduce compute cost without managing EC2 instances. The tasks can tolerate interruption and restart. Which option should the solutions architect recommend?

- My answer:
  - Fargate on-demand capacity only
    - on-demand fargate provides no interruption discount and is more expensive for interruption-tolerant batch tasks

- Correct:
  - Fargate spot capacity
    - uses spare fargate capacity at a lower price for interruption-tolerant tasks, without requiring EC2 host management

- Other alternatives:
  - dedicated hosts for the container tasks: used for licensing and compliance, not for a cost optimization for fault-tolerant batch tasks
  - ECS on EC2 with on-demand instances: requires managing the underlying instances

> Fargate spot for interruption-tolerant container tasks when you want fargate's serverless model at a lower cost

30. A company needs a relational database that can fail over to a second AWS Region with a recovery point objective of less than one second. The secondary Region must also support low-latency read traffic. Which solution meets these requirements?

- My answer:
  - An Amazon RDS cross-Region read replica.
    - cross-region read replicas use async replication and can have a higher RPO [recovery point objective] than one second. Also require manual promotion during failover

- Correct:
  - Amazon Aurora Gloabl db
    - replicates storage across regions with low latency and provides a RPO typically under a second, with read replicas in secondary regions

- Other alternatives:
  - Amazon S3 Cross-Region Replication for database backups: backs up objects, not a relational db and doesn't provide low-RPO failover target
  - An Amazon RDS Multi-AZ DB instance: provides high-availability within a single region. It doesn't provide a cross-region replica or a sub-second cross-region RPO

> Aurora global db for cross-region relational DR with low RPO and low-latency reads in secondary regions

37. A small development VPC has low outbound internet traffic, and high availability is not required. The company wants the lowest-cost way to provide internet access to private-subnet instances. Which solution is MOST cost-effective?

- My answer:
  - Deploy a single NAT Gateway in one Availability Zone.
    - a single NAT gateway is cheaper than multiple gateways but still carries hourly and data processing charges that can exceed a small NAT instance for low traffic

- Correct:
  - Run a NAT instance on a small EC2 instance
    - for a low-traffic, non-critical development VPC, a small NAT instance can be cheaper than a NAT gateway, at the cost of additional management and lower availability

- Other alternatives:
  - Create a gateway VPC endpoint for all internet destinations: gateway VPC endpoints support specific AWS services, not general internet access
  - Deploy a NAT Gateway in each Availability Zone: NAT gateways charge hourly and per GB processed. Multiple NAT gateways for a low-traffic development VPC is crazy expensive

> For low-traffic, non-critical VPCs, a small NAT instance can be cheaper than a NAT gateway; use NAT gateways for production availability

51. A company needs to catalog datasets in Amazon S3 and run Spark-based transformations for analytics. The company does not want to manage Spark clusters or infrastructure. Which service should the solutions architect use?

- My answer:
  - Amazon Athena
    - Athena is a serverless query service. It's not a Spark ETL service for complex transformations

- Correct:
  - AWS Glue with crawlers and ETL jobs
    - AWS glue crawlers populate the data catalog
    - serverless Glue ETL jobs run Spark transformatinos without cluster management

- Other alternatives:
  - Kinesis data firehose: delivers streaming data, it doesn't catalog datasets on run Spark ETL jobs
  - EMR on EC2: EMR can run Spark, but managing clusters adds operational overhead that the requirement explicitly excludes

> AWS Glue crawlers and ETL jobs for serverless data cataloging and spark-based transformations

53. A platform team wants application teams to create IAM roles for their AWS Lambda functions. The application teams must be able to create roles and attach policies, but they must never be able to grant a role more permissions than the platform team's approved maximum. Which TWO steps should the solutions architect take? (Choose two.)

- My answer:
  - wrong: Use IAM Access Analyzer to review roles after they are created and delete any role that exceeds the approved permissions.
    - detective and reactive. A role could be used before review, and access could already have been scalated
  - correct: Create an IAM permissions boundary policy that defines the maximum allowed permissions, and require every application-created role to include that boundary.
    - permission boundary sets the max permissinos that identity-based policies can grant a role, even if the role policy allows more

- Correct:
  - Grant application teams iam:CreateRole and iam:PutRolePermissionsBoundary, and add a condition that requires the permissions boundary ARN to match the approved policy.
    - enforcing the boundary at role creation and update time prevents teams from creating/modifying roles without the approved maximum

> Use permission boundaries plus a condition requiring the boundary on role creation to delete IAM admin safely

54. Account A stores objects in an Amazon S3 bucket using server-side encryption with an AWS KMS customer managed key (SSE-KMS) in Account A. An IAM role in Account B needs to download and decrypt those objects. The S3 bucket policy already allows the Account B role to perform s3:GetObject. Which TWO additional permissions are required? (Choose two.)

- My answer:
  - correct: KMS key policy in account A must allow the acount B role to the key for `kms:Decrypt`
    - A cross-account KMS key policy must grant the external principal access. s3 bucket policy alone doesn't authorize KMS decryption
  - wrong: the Account A service control policy must allow `kms:Decrypt` for Account B
    - SCPs don't grant permissions. They only limit the max permission available within an account

- Correct:
  - The IAM policy for the Account B role must allow `kms:Decrypt` on the account A key ARN
    - Cross-account access require both the key policy and the caller's identity policy to allow the action

- Other alternatives:

> For cross-account SSE-KMS access, allow the external principal in both the KMS key policy and the caller's IAM policy; the s3 bucket policy handles only s3 authorization

62. A company uses AWS Backup to protect Amazon S3 and Amazon RDS resources. Auditors require that backups cannot be deleted or altered during a 7-year retention period, even by administrators with full AWS Backup permissions. Which feature meets this requirement?

- My answer:
  - AWS Backup vault lock in governance mode
    - Governance mode can be bypassed by users with sufficient IAM permissinos, so it doesn't meet the admin-can't-delete requirement

- Correct:
  - AWS backup valut lock in compliance mode
    - Vault lock in compliance mode enforces write-once, read-many protection that even the account root user can't remove before the retention period ends, subject to the config time

- Other alternatives:

> Use AWS backup vault lock in compliance mode for immutable, admin-proof backups across AWS backup-supported resources
