# Amazon RDS PostgreSQL Lab

## Overview

This project demonstrates deploying a managed PostgreSQL database with Amazon RDS into private subnets, and connecting to it from an EC2 instance using an IAM role and AWS Secrets Manager instead of hardcoded credentials.

The environment was built for Ubuntu Tech Solutions, a fictional technology company that needs a backend relational database for internal applications without exposing that database directly to the internet or storing its password in plaintext.

## Business Scenario

Ubuntu Tech Solutions did not yet have a managed database for internal application data. This created several problems:

* No centralized, relational data store for applications such as an internal employee/department directory.
* Database credentials would otherwise need to be shared or hardcoded manually.
* A database with a public endpoint would be an unnecessary security risk.

To address this, an RDS PostgreSQL instance was deployed into private subnets that are not reachable from the internet, and the database password was stored in AWS Secrets Manager rather than in any script or configuration file. An EC2 instance retrieves that password at connection time using a scoped IAM role.

## Architecture

```text
                        VPC — ubuntu-tech-vpc (10.0.0.0/16)
   +--------------------------------------------------------------+
   |                                                                |
   |   Public Subnets (us-east-2a/2b)      Private Subnets (2a/2b) |
   |  +------------------------+        +--------------------------+
   |  |   EC2 instance         |        |   ubuntu-tech-rds        |
   |  |   (postgresql-client)  |------->|   PostgreSQL, db.t4g.micro|
   |  |                        | 5432   |   No public access        |
   |  +------------------------+        +--------------------------+
   |          |    ^                                                |
   |          |    | IAM role                                       |
   |          v    |                                                |
   |   Secrets Manager (DB password)                                |
   +--------------------------------------------------------------+
```

## AWS Services Used

* Amazon VPC (subnets, route tables)
* Amazon RDS (PostgreSQL)
* DB Subnet Groups
* Security Groups
* AWS Secrets Manager
* IAM Roles
* Amazon EC2
* Amazon CloudWatch (RDS monitoring)

## Implementation

### 1. Network Preparation

The existing VPC (`ubuntu-tech-vpc`, `10.0.0.0/16`) already had public and private subnets across two Availability Zones. A dedicated private route table (`ubuntu-tech-db-private-rt`) was created and explicitly associated with both private subnets, keeping database traffic off the public route table.

*(`01-vpc-resource-map-subnets-routes.png`, `04-db-private-route-table-created.png`, `05-route-table-subnet-associations.png`)*

### 2. DB Subnet Group

A DB subnet group (`ubuntu-tech-db-subnet-group`) was created spanning the two private subnets (`us-east-2a`, `us-east-2b`), so RDS can place the instance without any public exposure.

*(`02-db-subnet-group-created.png`)*

### 3. Security Group

A dedicated security group (`ubuntu-tech-rds-sg`) was created, allowing inbound PostgreSQL traffic (TCP 5432) only from specific EC2 security groups rather than from any IP range.

*(`03-rds-security-group-created.png`, `08-security-group-rules-ec2-inbound.png`)*

### 4. RDS Instance

The `ubuntu-tech-rds` PostgreSQL instance (`db.t4g.micro`, single-AZ, `us-east-2a`) was launched using the subnet group and security group above, with a connection set up between the instance and the EC2 client. Credentials are managed through Secrets Manager rather than IAM database authentication.

*(`06-rds-instance-created-connected-ec2.png`, `07-rds-connection-code-snippet.png`)*

### 5. Connecting From EC2

The PostgreSQL client was installed on the EC2 instance, and the RDS TLS certificate bundle was downloaded so the connection could use `sslmode=verify-full`. The first connection attempt failed with an IAM credentials error — see `troubleshooting.md`, Issue 1. An IAM role scoped to read-only Secrets Manager access was created and attached to the instance, verified with `aws sts get-caller-identity`, after which the connection succeeded.

*(`09-ec2-install-postgresql-client.png`, `10-ec2-download-cert-set-rdshost.png`, `11-psql-connect-failed-nocredentials.png`, `12-iam-role-created-secrets-access.png`, `13-iam-role-attached-to-ec2.png`, `14-verify-role-sts-get-caller-identity.png`)*

### 6. Database Validation

An `employees` table was created and populated with sample IT/HR records, then queried successfully over the encrypted connection — confirming the database is reachable, writable, and readable end-to-end from the authorized instance.

*(`15-psql-create-insert-select-success.png`)*

### 7. Credential Handling on a Second Instance

A second EC2 instance without the IAM role attached was used to re-run the same connection command, and it reproduced the identical Secrets Manager credentials error from Issue 1.

**Not pictured / caveat:** this does not test whether `ubuntu-tech-rds-sg` blocks network access from that instance — the command fails at the AWS CLI/Secrets Manager step, before `psql` ever attempts to reach the database over the network. See `troubleshooting.md`, Issue 2, for what this screenshot does and doesn't demonstrate.

*(`16-second-instance-same-credentials-error.png`)*

### 8. Monitoring and Configuration Review

The instance's status, DB Load metric, and backup/maintenance settings (7-day automated backups, defined backup and maintenance windows) were reviewed in the RDS console.

*(`17-rds-monitoring-status-available.png`, `18-rds-db-load-metrics.png`, `19-rds-maintenance-backups-config.png`)*

## Validation

The completed environment was validated by checking:

* The RDS instance reached the `Available` status with the expected engine (PostgreSQL) and instance class.
* The database subnet group and route table placed the instance entirely within private subnets.
* The security group restricted inbound access to specific EC2 security groups on port 5432 only.
* An authorized EC2 instance could install a client, retrieve the DB password from Secrets Manager via an IAM role, and connect using TLS.
* Data could be created, inserted, and queried successfully (`employees` table).
* Automated backups were enabled with a defined backup window.

## Key Concepts Demonstrated

### Private Subnet Isolation

The database has no public endpoint and lives only in private subnets, reducing its exposure to the internet.

### Credential Management via Secrets Manager

The database password is never stored in a script or on disk — it is fetched at connection time from Secrets Manager.

### IAM Roles for EC2

Rather than embedding AWS credentials on the instance, an IAM role scoped to read-only Secrets Manager access was attached directly to the EC2 instance.

### Defense in Depth

Reaching the database requires clearing two independent layers: network-level access (security group) and AWS API authorization (IAM role for Secrets Manager). Issue 2 in `troubleshooting.md` discusses why a single failure doesn't confirm both layers at once.

## Lessons Learned

The main lesson from this lab was that an EC2 instance needs an explicit IAM role before it can call other AWS service APIs — attaching a security group alone is not enough to authorize the instance to read the database password from Secrets Manager. Full detail on this diagnostic process is in `troubleshooting.md`, Issue 1.

A secondary lesson, covered in Issue 2, was to be careful about what a given error actually proves: a Secrets Manager credentials failure looks similar to a network connectivity failure in the terminal, but they are different layers and require different fixes.

## Conclusion

The completed environment demonstrates a managed relational database that is isolated from the public internet and accessed only through IAM-authorized, credential-free connections from application servers. This gives Ubuntu Tech Solutions a repeatable pattern for standing up backend databases without hardcoding secrets, and a foundation for adding read replicas, Multi-AZ failover, or IAM database authentication in future labs.
