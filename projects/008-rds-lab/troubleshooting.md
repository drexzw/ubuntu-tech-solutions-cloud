# RDS PostgreSQL Lab — Troubleshooting

This document records problems encountered while configuring and testing the Ubuntu Tech Solutions RDS PostgreSQL environment.

---

# Issue 1 — EC2 Could Not Connect to RDS

## Problem

The Ubuntu EC2 instance was initially unable to establish the expected connection to the Amazon RDS PostgreSQL database.

The initial assumption was that the database should be accessible because the EC2 instance and RDS database had been configured within the AWS environment.

However, the connection attempt was unsuccessful.

---

## Symptoms

The PostgreSQL connection from the EC2 instance did not work as expected.

The problem required checking more than just the PostgreSQL username and password.

The following layers were investigated:

1. EC2 configuration
2. IAM permissions
3. VPC networking
4. RDS security group
5. PostgreSQL configuration and authentication

---

## Investigation

### Step 1 — Check the RDS Endpoint

The RDS endpoint was confirmed and used as the database host.

The PostgreSQL connection format was:

```bash
psql -h <RDS-ENDPOINT> -U postgres -d postgres
```

---

### Step 2 — Check PostgreSQL Port

PostgreSQL uses TCP port:

```text
5432
```

The RDS security group was checked to ensure that PostgreSQL traffic was permitted.

The intended security model was to allow PostgreSQL traffic from the authorized EC2 security group rather than opening the database to the entire internet.

---

### Step 3 — Check EC2 Permissions

The EC2 instance's AWS permissions were investigated.

The EC2 instance did not initially have the required IAM role associated with it.

An IAM role was therefore created/configured and attached to the EC2 instance.

After the role was attached, the EC2 instance had the AWS permissions required for the lab.

---

## Resolution

An IAM role was attached to the EC2 instance.

After correcting the EC2 permissions and verifying the RDS/network configuration, the PostgreSQL connection was successful.

The EC2 instance was able to connect to the RDS PostgreSQL server using `psql`.

A successful PostgreSQL session displayed:

```text
postgres=>
```

---

# Important Concept — IAM vs Security Groups vs PostgreSQL Authentication

One of the main lessons from this issue was that AWS access is controlled by multiple layers.

### IAM

IAM controls what an AWS identity is allowed to do.

```text
EC2 → IAM → AWS permissions
```

### Security Groups

Security groups control network traffic.

```text
EC2 → Security Group → TCP 5432 → RDS
```

### PostgreSQL Authentication

PostgreSQL controls database-level authentication.

```text
PostgreSQL User + Password → Database
```

These controls solve different problems.

A successful connection requires the relevant layers to be configured correctly.

---

# Issue 2 — Verifying Database Access Restrictions

## Problem

The lab required more than simply demonstrating that the authorized EC2 instance could connect.

The database should also reject connections from an unauthorized resource.

This was important for demonstrating that the RDS database was not simply exposed to every resource that could reach the AWS environment.

---

## Expected Security Model

```text
Authorized EC2
      |
      | Allowed
      v
RDS PostgreSQL
      ^
      |
      X
Unauthorized EC2
      |
      | Blocked
```

The RDS security group should allow PostgreSQL traffic from the authorized EC2 security group.

An EC2 instance that does not meet that security-group requirement should not be able to establish the network connection to PostgreSQL.

---

# Troubleshooting Checklist

When an EC2 instance cannot connect to an RDS PostgreSQL database, check the following in order.

## 1. RDS Status

Confirm that the RDS instance is:

```text
Available
```

---

## 2. RDS Endpoint

Confirm that the correct RDS endpoint is being used.

```bash
psql -h <RDS-ENDPOINT> -U postgres -d postgres
```

---

## 3. PostgreSQL Port

Confirm that PostgreSQL is using:

```text
TCP 5432
```

---

## 4. Security Group

Check the RDS inbound rules.

The preferred configuration for this lab is:

```text
Type: PostgreSQL
Protocol: TCP
Port: 5432
Source: EC2 Security Group
```

Avoid unnecessarily using:

```text
0.0.0.0/0
```

for a database.

---

## 5. VPC and Subnets

Confirm that the EC2 instance and RDS instance have network connectivity through the configured VPC.

Check:

* VPC
* Subnets
* Route tables
* Security groups
* Network ACLs if applicable

---

## 6. DNS Resolution

From the EC2 instance:

```bash
nslookup <RDS-ENDPOINT>
```

The endpoint should resolve to an IP address.

---

## 7. Test Port 5432

From the EC2 instance:

```bash
nc -zv <RDS-ENDPOINT> 5432
```

If the port is reachable, the result should indicate a successful TCP connection.

---

## 8. IAM Identity

If AWS permissions are involved, verify the identity being used by the EC2 instance:

```bash
aws sts get-caller-identity
```

This can help determine whether the EC2 instance is operating with the expected IAM role.

---

## 9. PostgreSQL Authentication

If network connectivity works but `psql` still fails, check:

* Username
* Password
* Database name
* RDS endpoint
* PostgreSQL port

Example:

```bash
psql -h <RDS-ENDPOINT> -U postgres -d postgres
```

---

# Lessons Learned

This troubleshooting exercise demonstrated that cloud database connectivity should be diagnosed in layers rather than assuming that a single AWS configuration controls access.

The main troubleshooting sequence was:

```text
EC2
 ↓
IAM permissions
 ↓
VPC/network
 ↓
Security Group
 ↓
TCP 5432
 ↓
RDS
 ↓
PostgreSQL authentication
 ↓
Database
```

The most important lesson was that **network access, AWS permissions, and database authentication are separate controls**.

The issue was resolved by correcting the EC2 IAM configuration and verifying the surrounding RDS network configuration.

This provided practical experience troubleshooting a managed AWS database rather than simply deploying one.
