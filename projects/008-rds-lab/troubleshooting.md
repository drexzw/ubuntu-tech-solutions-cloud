# Troubleshooting

This document records troubleshooting performed during the RDS PostgreSQL lab.

The purpose of documenting these issues is to show the actual diagnostic process used during deployment rather than only documenting the final successful configuration.

---

## Issue 1: NoCredentials Error Connecting to RDS from EC2

### Symptom

The first attempt to connect to the database from the EC2 instance failed. The embedded AWS CLI call inside the `psql` command returned:

```text
aws: [ERROR]: An error occurred (NoCredentials): Unable to locate credentials. You can configure credentials by running "aws login".
```

Because the password subshell failed, `psql` had no password to send and fell back to an interactive prompt, then failed to connect:

```text
psql: error: connection to server at "ubuntu-tech-rds.cvsu60uas78f.us-east-2.rds.amazonaws.com" (10.0.3.42), port 5432 failed: fe_sendauth: no password supplied
```

**Evidence:** `11-psql-connect-failed-nocredentials.png`

### Investigation

The EC2 instance had no IAM role attached. Without one, the AWS CLI running on the instance had no credentials to call Secrets Manager and retrieve the database password — this failure happens before any network connection to RDS is even attempted.

### Resolution

An IAM role, `Ubuntu-Tech-RDS-EC2-Role`, was created with the `AWSSecretsManagerClientReadOnlyAccess` managed policy (`12-iam-role-created-secrets-access.png`) and attached to the instance (`13-iam-role-attached-to-ec2.png`). The active role was confirmed with `aws sts get-caller-identity`, which returned an assumed-role ARN for `Ubuntu-Tech-RDS-EC2-Role` (`14-verify-role-sts-get-caller-identity.png`). Re-running the identical `psql` command then succeeded, and the resulting session was used to create and query the `employees` table (`15-psql-create-insert-select-success.png`).

### Lesson

An EC2 instance calling another AWS service's API — such as Secrets Manager — needs an IAM role explicitly granting that permission. A security group only controls network traffic; it has no bearing on whether the instance is authorized to call AWS APIs. Avoid embedding database passwords directly in commands or scripts — retrieving them through an IAM role and Secrets Manager keeps the password out of shell history and script files.

---

## Issue 2: Same Error Reproduced on a Second Instance

### Symptom

A second EC2 instance (`ip-172-31-41-176`) ran the identical `export RDSHOST=...` / `psql` command and hit the same `NoCredentials` error seen in Issue 1.

**Evidence:** `16-second-instance-same-credentials-error.png`

### What This Does and Doesn't Show

This screenshot is evidence that the same missing-IAM-role failure reproduces on a different instance — it is not evidence about network-level access control. The command fails at the `aws secretsmanager get-secret-value` step inside the password subshell, before `psql` ever attempts to open a connection to the RDS instance. So this screenshot does not confirm or rule out whether `ubuntu-tech-rds-sg` would have allowed or blocked this instance at the network layer.

**Not pictured:** whether this second instance's security group is even one of the entries permitted in `ubuntu-tech-rds-sg`'s inbound rules (`08-security-group-rules-ec2-inbound.png` lists the allowed groups), and whether this instance is in the same VPC as the RDS instance at all — its IP (`172.31.x.x`) falls in the AWS default-VPC CIDR range, not the `10.0.0.0/16` range used by `ubuntu-tech-vpc`. A real network-level access-control test would require attaching valid credentials (or the IAM role) to this instance first, and then observing whether the `psql` connection itself succeeds, is refused, or times out.

### Lesson

IAM authorization and network reachability are two independent layers of access control, and a failure at one layer can look identical to a failure at the other in a terminal. Confirming that a connection is blocked requires isolating which layer actually rejected it, rather than assuming a security-group boundary from a credentials error.

---

## Troubleshooting Approach

The main troubleshooting approach used in this lab was:

1. Identify the exact symptom and the exact command that produced it.
2. Read the error message closely to determine which layer failed (AWS CLI/IAM vs. network/`psql`).
3. Check whether the instance has the IAM permissions the command depends on.
4. Verify the fix with an independent command (`aws sts get-caller-identity`) before retrying the original operation.
5. Avoid attributing a result to a layer (e.g., security groups) that the evidence doesn't actually test.
