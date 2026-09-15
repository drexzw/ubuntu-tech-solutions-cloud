# Amazon RDS PostgreSQL Database Lab

## Overview

This lab demonstrates how Ubuntu Tech Solutions can use **Amazon Relational Database Service (Amazon RDS)** to host a managed PostgreSQL database instead of running the database directly on an EC2 instance.

The lab focuses on:

* Creating a PostgreSQL RDS database
* Configuring network access between EC2 and RDS
* Connecting to the database from an authorized EC2 instance
* Creating and managing a PostgreSQL database
* Troubleshooting failed database connectivity
* Using an IAM role with the EC2 instance where required
* Testing access from an unauthorized source

### Business Scenario

Ubuntu Tech Solutions is developing a cloud-based application that requires a reliable relational database.

Instead of managing PostgreSQL directly on an EC2 server, the company uses Amazon RDS to provide a managed database service.

This allows the company to focus on the application while AWS handles much of the underlying database infrastructure, including database provisioning, backups, patching, and infrastructure management.

---

## Architecture

The lab uses the following architecture:

```text
                    AWS Cloud
                        |
              +---------+---------+
              |                   |
          EC2 Instance         Amazon RDS
          Ubuntu Server       PostgreSQL
              |                   |
              +------ Network -----+
```

The EC2 instance acts as the authorized client used to administer and test the PostgreSQL database.

The RDS instance hosts the PostgreSQL database.

---

## Technologies Used

* Amazon RDS
* PostgreSQL
* Amazon EC2
* Amazon VPC
* Security Groups
* AWS IAM
* Ubuntu Linux
* PostgreSQL CLI (`psql`)

---

# Lab Objectives

By completing this lab, I demonstrated the ability to:

1. Deploy a managed PostgreSQL database using Amazon RDS.
2. Configure network access between EC2 and RDS.
3. Connect to an RDS PostgreSQL database from Linux.
4. Create and manage PostgreSQL databases.
5. Troubleshoot database connectivity problems.
6. Configure an IAM role for an EC2 instance when required.
7. Verify that database access is restricted to authorized resources.

---

# 1. Create the RDS PostgreSQL Database

An Amazon RDS PostgreSQL instance was created using the AWS Management Console.

The database was configured to run PostgreSQL and placed within the appropriate VPC and subnet configuration.

The RDS security group was configured to allow PostgreSQL traffic on:

```text
TCP 5432
```

Access was restricted to the appropriate EC2 security group rather than allowing PostgreSQL access from the entire internet.

### Why port 5432?

PostgreSQL uses TCP port **5432** by default.

---

# 2. Configure EC2 Connectivity

The Ubuntu EC2 instance was used as the authorized client for connecting to the RDS database.

The EC2 instance and RDS database were configured so that the EC2 instance could communicate with the RDS instance through the VPC.

The RDS security group allowed inbound PostgreSQL traffic from the EC2 security group.

This is preferable to allowing:

```text
0.0.0.0/0
```

because database access should not be publicly available unless there is a specific requirement for it.

---

# 3. Install PostgreSQL Client

The PostgreSQL client tools were installed on the Ubuntu EC2 instance.

The `psql` command-line client was then used to connect to the RDS PostgreSQL server.

Example:

```bash
psql -h <RDS-ENDPOINT> -U postgres -d postgres
```

The RDS endpoint was used as the database host.

---

# 4. Initial Connectivity Problem

During testing, the EC2 instance was initially **unable to connect to the RDS database**.

This was an important troubleshooting step because the problem demonstrated that simply having an EC2 instance and an RDS database in AWS does not automatically mean that the EC2 instance has the required permissions or access.

The connection attempt failed rather than immediately providing access to the database.

This led to troubleshooting the EC2 instance's AWS permissions and configuration.

---

# 5. Attach an IAM Role to EC2

As part of troubleshooting, an **IAM role was created/attached to the EC2 instance**.

The role provided the EC2 instance with the AWS permissions required for the lab.

After the role was attached, the EC2 instance was able to perform the required AWS-authenticated operations.

### Important distinction

IAM controls **AWS-level permissions**.

The RDS security group controls **network-level access**.

PostgreSQL authentication controls **database-level access**.

Therefore, successful RDS connectivity can involve multiple layers:

```text
EC2
 |
 | AWS permissions
 v
IAM
 |
 | Network access
 v
VPC / Security Group
 |
 | PostgreSQL connection
 v
RDS PostgreSQL
 |
 | Database authentication
 v
PostgreSQL User
```

This troubleshooting process demonstrated why cloud connectivity problems need to be checked layer by layer rather than assuming that one configuration controls everything.

---

# 6. Connect to PostgreSQL

After the EC2 configuration was corrected, the PostgreSQL client was used to connect to the RDS instance.

Once connected, the PostgreSQL prompt appeared:

```text
postgres=>
```

This confirmed that the EC2 instance could successfully communicate with the RDS PostgreSQL database.

---

# 7. Create the Application Database

A database was created for the Ubuntu Tech Solutions application:

```sql
CREATE DATABASE ubuntu_tech_db;
```

PostgreSQL returned:

```text
CREATE DATABASE
```

The new database was then selected:

```sql
\c ubuntu_tech_db
```

The PostgreSQL prompt changed to indicate that the session was connected to:

```text
ubuntu_tech_db
```

This demonstrated that the RDS PostgreSQL instance was functioning as the database server for the application.

---

# 8. Database Connectivity Testing

The lab also included testing whether database access was restricted to authorized resources.

The objective was not simply to prove that the authorized EC2 instance could connect.

The objective was also to verify that an unauthorized resource should **not** be able to connect.

The expected security model was:

```text
Authorized EC2
       |
       | Allowed
       v
RDS PostgreSQL


Unauthorized EC2
       |
       | Blocked
       X
RDS PostgreSQL
```

Testing access from another EC2 instance provides a practical way to demonstrate this restriction.

The unauthorized instance should fail to establish the PostgreSQL connection when it does not meet the RDS security group's access requirements.

---

# 9. Security Considerations

The RDS database was intentionally not configured to allow unrestricted internet access.

The security group should follow the principle of least privilege.

Instead of:

```text
PostgreSQL
Source: 0.0.0.0/0
```

the preferred configuration is:

```text
PostgreSQL
Port: 5432
Source: EC2 Security Group
```

This means that only resources associated with the authorized security group can reach the database over PostgreSQL's network port.

Additional security is provided by PostgreSQL authentication and AWS IAM permissions where applicable.

---

# Troubleshooting

## Problem: EC2 could not connect to RDS

### Symptoms

The EC2 instance was unable to establish the expected connection to the RDS PostgreSQL database.

### Investigation

The configuration was checked across multiple layers:

1. EC2 configuration
2. IAM permissions
3. RDS security group
4. VPC/network configuration
5. PostgreSQL connection parameters

### Resolution

An IAM role was attached to the EC2 instance to provide the required AWS permissions.

After the EC2 permissions were corrected and the network configuration was in place, the EC2 instance was able to connect to the RDS PostgreSQL database.

### Lesson Learned

Cloud connectivity is not controlled by a single AWS setting.

When troubleshooting RDS connectivity, it is important to separate:

* **IAM permissions**
* **Security group rules**
* **VPC/network connectivity**
* **Database authentication**
* **Database configuration**

Checking these individually makes it much easier to identify the actual cause of a connection failure.

---

# Key Commands

### Connect to PostgreSQL

```bash
psql -h <RDS-ENDPOINT> -U postgres -d postgres
```

### Create the application database

```sql
CREATE DATABASE ubuntu_tech_db;
```

### Connect to the application database

```sql
\c ubuntu_tech_db
```

### View databases

```sql
\l
```

### Exit PostgreSQL

```sql
\q
```

---

# Skills Demonstrated

This lab demonstrates practical experience with:

* AWS RDS
* PostgreSQL
* EC2
* IAM
* VPC networking
* Security groups
* Linux command line
* Database administration
* Cloud troubleshooting
* Least-privilege access
* AWS infrastructure troubleshooting

---

# Business Outcome

Ubuntu Tech Solutions now has a managed PostgreSQL database that can be accessed by authorized application infrastructure without requiring the company to manage the underlying database server directly.

The lab also demonstrated how access can be restricted to trusted AWS resources and how connectivity problems can be diagnosed across the IAM, networking, security group, and database layers.

This provides a foundation for connecting future cloud applications to a managed relational database.
