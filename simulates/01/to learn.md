# To learn

- Amazon Macie? Other new services I haven't learned on the course?
- partition placement group vs. cluster placement group?
- NACL: what it is, why is it stateless?
- What is RPO?
- What is RTO?
- What is a NAT instance vs. NAT gateway?
- Important S3 services
  - macie
  - aws glue
  - storage lens
  - what else? What other services are processing stuff from s3?

- IAM permissions
  - What is ARN?

- cross-account KMS

## Amazon Macie

- Fully managed data security and data privacy service that uses ML and pattern matching to automatically discover, classify and protect sensitive data stored in S3
- Scan S3 buckets for PII, without writing a custom regex, go with Macie

## What is RPO? [Recovery point objective - how much data can we lose?]

- Recovery point objective
- Maximum acceptable amount of data loss measured in time when an outage or disaster occurs
- How far back in time we can rollout the data in case of failure. Basically, "how much data can we afford to lose?"
- If you take db backups once every hour, your RPO is 1h

## What is RTO? [Recovery time objective - how long can we be offline?]

- Recovery time objective
- Maximum acceptable amount of downtime after an outage or disaster occurs
- How quickly you must restore your application, infra, and network

## Important S3 services

- See 00. intro/19. S3 services.md

## IAM permissions

- To delegate IAM role creation to devs without letting them scalate their own privileges, it's necessary to combine:
  - IAM permission boundaries
  - strict condition at role creation time

- IAM permission boundary
  - maximum ceiling of permissions an IAM role can have
  - doesn't grant permissions, it's only a cap

- Step 1: define the ceiling
  - platform team defines the ceiling, maximum approved permissions

- Step 2: enforce the ceiling preventively
  - simply creating the boundary do nothing unless devs are forced to attach it
  - without the attached boundary, they can't create the role
  - you can also prevent the role from being deleted after

### ARN [Amazon Resource Name]

- Unique, globally uniform identifier AWS assigns to every resource in your account
- IAM policy ARN looks like this:

```
arn:aws:iam::<number>:policy/PlatformApprovedLambdaBoundary
```

- arn:aws:iam:: -> identifies the AWS IAM service
  - global, so the region field between the colons is blank
- <number>: -> 12-digit AWS account ID
- policy/PlatformApprovedBoundary: resource type `policy` and the exact name of the managed policy created by the platform team

## Cross-account KMS

- two-key rule: both the account owning the resource and the account calling must explicitly agree
  - for an s3 object encrypted with a customer-managed KMS key [SSE-KMS], data is protected by two separate resources in account A [s3 object + KMS KEY]
  - two-layer auth when account B tries to reach it

### same vs. cross-account encryption

- same-account: within a single AWS account, IAM trusts its own resources
  - an `allow` in either the IAM identity policy or resource policy [like s3 bucket policy or KMS key policy] is enough to grant access, provided there's no explicit `deny`

- cross-account: AWS never allows one account to unilaterally grant itself access to another account's resources, not does it allow external account for force permissions onto a principal you own
  - thus, access requires explicit `allow` on BOTH SIDES of the account boundary
  - resource side [account a]: I trust account b to user my resource
  - identity side [account b]: I permit my account to reach out and use that specific resource in account A

## Cost optimization

- AWS storage lens vs. Trusted advisor
  - storage lens: organization-wide visibility, dashboard for s3 for all accounts
  - trusted advisor: runs broader account-level best practices checks, rather than deep s3 usage analytics
    - iddle EC2 instances, unattached EBS volumes

- Fargate spot vs. on-demand
  - whenever a a scenario asks to reduce container compute costs for fault-tolerant, interruptible batch jobs without managing ev2 hosts, choose fargate spot [up to 70% off on-demand, with a 2-min interruption notice]
  - on-demand: for critical, interruption-sensitive workloads

- NAT instance vs. NAT Gateway:
  - NAT Gateway: default for prod because they're managed and highly available, but they carry fixed hourly price [$32/mo] and per-GB processing fees
  - if a question explicitly specifies a low-traffic dev/test VPC where high availability isn't required and asks for the lowest cost, a small EC2 NAT instance is the way to go

## Security, IAM and governance

- SCPS never grante permissions [cross-account KMS]
  - SCPS [service control policies] only define the max permission boundary for an account
    - never grant permissinos
  - cross-account decryption of SSE-KMS objects, you always need an explicit `Allow` in both the KMS key policy [account A] and the caller's IAM policy [account B]

- Preventive vs. detective guardrails [permissions boundaries]:
  - detective: AWS access analyzer, AWS config, and GuardDuty
    - alert/remediate AFTER an action happens
  - preventive: IAM permissions boundaries
    - prevent privelege scalation when delegating role creation

- Compliance mode vs. Governance mode [WORM]:
  - governance mode: can be bypassed by users with special IAM permissions
  - compliance mode: no one can delete/shorten retention, not even admins or root users

- Macie vs. Inspector:
  - Macie: scans S3 with ML/pattern matching to discover and classify PII
  - Inspector: scans EC2 instances, ECR container images and lambda func for software vulnerabilities
