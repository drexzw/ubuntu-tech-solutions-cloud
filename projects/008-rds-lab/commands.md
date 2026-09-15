# Ubuntu Tech Solutions — RDS Connectivity Commands

This document lists the commands run on the EC2 client instance to install a PostgreSQL client, connect securely to the `ubuntu-tech-rds` instance, and validate the database. For the reasoning behind the IAM role step, see `troubleshooting.md`.

## 1. Check for a PostgreSQL Client

```bash
psql --version
```

### Purpose

Confirms whether a PostgreSQL client is already installed. On this instance it was not.

---

## 2. Install the PostgreSQL Client

```bash
sudo apt install postgresql-client-common
```

### Purpose

Installs the client package suggested by Ubuntu after the missing-`psql` check.

---

## 3. Download the RDS TLS Certificate Bundle

```bash
curl -o global-bundle.pem https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem
```

### Purpose

Downloads Amazon's certificate bundle so the connection can be validated with `sslmode=verify-full` instead of connecting without certificate verification.

---

## 4. Set and Confirm the RDS Endpoint

```bash
export RDSHOST="ubuntu-tech-rds.cvsu60uas78f.us-east-2.rds.amazonaws.com"
echo $RDSHOST
```

### Purpose

Stores the RDS endpoint in an environment variable for reuse in the connection command below.

---

## 5. Connect to the Database

```bash
psql "host=$RDSHOST port=5432 dbname=postgres user=drex sslmode=verify-full sslrootcert=./global-bundle.pem password=$(aws secretsmanager get-secret-value --secret-id 'arn:aws:secretsmanager:us-east-2:858758523801:secret:rds!db-b920854c-e5f3-4d11-aabc-09e19e25be8d-cs3jqH' --query SecretString --output text | jq -r '.password')"
```

### Purpose

Connects to PostgreSQL over TLS, fetching the database password from Secrets Manager at run time instead of typing or storing it. This command failed the first time it was run — see `troubleshooting.md`, Issue 1 — and succeeded after an IAM role was attached to the instance.

### Parameters

* `sslmode=verify-full` — validates the server certificate against `sslrootcert`, protecting against a spoofed endpoint.
* `password=$(...)` — a subshell that calls Secrets Manager and extracts the `password` field with `jq`.

---

## 6. Verify the Instance's IAM Identity

```bash
aws sts get-caller-identity
```

### Purpose

Confirms which IAM identity the instance is currently using. Used to verify that `Ubuntu-Tech-RDS-EC2-Role` was actually attached and assumed before retrying the connection.

---

## 7. Create and Query Test Data

```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    role VARCHAR(100)
);

INSERT INTO employees (name, department, role)
VALUES
    ('John Smith', 'IT', 'Help Desk Technician'),
    ('Sarah Johnson', 'HR', 'HR Specialist'),
    ('Michael Brown', 'IT', 'Cloud Administrator');

SELECT * FROM employees;
```

### Purpose

Validates that the connection supports full read/write access: creating a table, inserting rows, and reading them back.

## Command Summary

| Command | Purpose |
| --- | --- |
| `psql --version` | Check for an existing PostgreSQL client |
| `sudo apt install postgresql-client-common` | Install the PostgreSQL client package |
| `curl -o global-bundle.pem ...` | Download the RDS TLS certificate bundle |
| `export RDSHOST=... / echo $RDSHOST` | Set and confirm the RDS endpoint |
| `psql "host=$RDSHOST ..."` | Connect to RDS using a password fetched from Secrets Manager |
| `aws sts get-caller-identity` | Confirm the instance's active IAM role |
| `CREATE TABLE / INSERT / SELECT` | Create and validate test data in PostgreSQL |
