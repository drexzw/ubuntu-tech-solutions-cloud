# RDS PostgreSQL Lab — Commands

This file contains the commands used during the Ubuntu Tech Solutions RDS PostgreSQL lab.

---

## PostgreSQL Client

### Connect to the RDS PostgreSQL Instance

```bash
psql -h <RDS-ENDPOINT> -U postgres -d postgres
```

### Connect to a Specific Database

```bash
psql -h <RDS-ENDPOINT> -U postgres -d ubuntu_tech_db
```

### Exit PostgreSQL

```sql
\q
```

---

# PostgreSQL Database Management

### Display Available Databases

```sql
\l
```

### Create the Ubuntu Tech Solutions Database

```sql
CREATE DATABASE ubuntu_tech_db;
```

Expected output:

```text
CREATE DATABASE
```

### Connect to the Application Database

```sql
\c ubuntu_tech_db
```

### Display Current Connection Information

```sql
\conninfo
```

This shows which database, user, host, and port the current PostgreSQL session is using.

### Display Available Tables

```sql
\dt
```

---

# PostgreSQL Testing

### Test the Current Database Connection

```sql
SELECT current_database();
```

Expected result:

```text
ubuntu_tech_db
```

### Test the Current PostgreSQL User

```sql
SELECT current_user;
```

### Test the PostgreSQL Server Version

```sql
SELECT version();
```

---

# Linux Network Troubleshooting

These commands can be used from the EC2 Ubuntu instance when troubleshooting connectivity to RDS.

### Test DNS Resolution

```bash
nslookup <RDS-ENDPOINT>
```

or:

```bash
dig <RDS-ENDPOINT>
```

### Test TCP Connectivity to PostgreSQL

```bash
nc -zv <RDS-ENDPOINT> 5432
```

A successful result indicates that the EC2 instance can reach the RDS endpoint on PostgreSQL's TCP port.

### Test Connectivity Using Telnet

```bash
telnet <RDS-ENDPOINT> 5432
```

This can also be used to determine whether TCP port 5432 is reachable.

---

# AWS CLI

If the AWS CLI is configured on the EC2 instance, the following command can be used to verify the identity associated with the instance:

```bash
aws sts get-caller-identity
```

This is useful when troubleshooting IAM permissions because it shows which AWS identity the EC2 instance is using.

### Check the AWS Region

```bash
aws configure get region
```

---

# Useful PostgreSQL Port Information

| Service    | Protocol | Port |
| ---------- | -------- | ---: |
| PostgreSQL | TCP      | 5432 |

The RDS security group must allow the EC2 instance to reach TCP port `5432`.

---

# Command Summary

| Purpose              | Command                                          |
| -------------------- | ------------------------------------------------ |
| Connect to RDS       | `psql -h <RDS-ENDPOINT> -U postgres -d postgres` |
| List databases       | `\l`                                             |
| Create database      | `CREATE DATABASE ubuntu_tech_db;`                |
| Connect to database  | `\c ubuntu_tech_db`                              |
| Show connection info | `\conninfo`                                      |
| List tables          | `\dt`                                            |
| Current database     | `SELECT current_database();`                     |
| Current user         | `SELECT current_user;`                           |
| PostgreSQL version   | `SELECT version();`                              |
| Test DNS             | `nslookup <RDS-ENDPOINT>`                        |
| Test port 5432       | `nc -zv <RDS-ENDPOINT> 5432`                     |
| Check AWS identity   | `aws sts get-caller-identity`                    |
| Exit PostgreSQL      | `\q`                                             |
