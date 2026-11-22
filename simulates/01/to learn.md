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
