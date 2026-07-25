# Project 001: Employee Identity & Access Management

## Project Overview

Ubuntu Tech Solutions is expanding its cloud infrastructure and has hired employees across different departments. 

As the Junior Cloud Administrator, I was tasked with designing a secure identity and access management system that provides employees with the correct AWS permissions based on their job responsibilities.

The goal was to implement role-based access control while following the Principle of Least Privilege.

---

# Business Problem

As the company grows, employees require different levels of access to AWS resources.

The company needed a solution where:

- Cloud administrators could manage AWS infrastructure.
- Help Desk technicians could view resources for troubleshooting.
- Interns had limited access to training resources.
- Employees did not receive unnecessary permissions.

Managing permissions individually would become difficult and create security risks.

---

# Solution Implemented

AWS Identity and Access Management (IAM) was implemented to control employee access.

The solution included:

- Creating IAM users for employees.
- Creating IAM groups based on job roles.
- Assigning AWS managed policies to groups.
- Applying role-based permissions.
- Testing user access levels.

---

# IAM Structure
<img width="355" height="304" alt="image" src="https://github.com/user-attachments/assets/ae62fb17-d601-42cd-90f5-1e6e4111791f" />

---

# AWS Services Used

| Service | Purpose |
|---|---|
| AWS IAM | Identity and access management |
| IAM Users | Individual employee identities |
| IAM Groups | Organizing users by role |
| IAM Policies | Defining permissions |

---

# Security Practices Applied

## Principle of Least Privilege

Users were only given permissions required for their job responsibilities.

## Group-Based Permissions

Permissions were assigned through groups instead of individual users, improving scalability and management.

## Access Separation

Each department's access was scoped so that one group could not view or modify resources belonging to another.

- **Cloud Administrators** had full IAM, EC2, S3, and VPC permissions needed to manage infrastructure.
- **Help Desk Technicians** were given read-only access (e.g. `ReadOnlyAccess`) so they could view resource status for troubleshooting without being able to modify or delete anything.
- **Interns** were restricted to a narrow set of permissions covering only training/sandbox resources, with no access to production infrastructure.

This separation ensures that a compromised or misused account in one group cannot affect resources outside its intended scope, limiting the "blast radius" of any single account.

---

# Steps Taken

1. **Planned the role structure** — mapped each job title (Cloud Administrator, Help Desk Technician, Intern) to the AWS permissions it actually needed, based on daily responsibilities.
2. **Created IAM groups** for each role: `CloudAdmins`, `HelpDesk`, `Interns`.
3. **Attached policies to groups** rather than individual users:
   - `CloudAdmins` → AWS managed policies covering EC2, S3, VPC, and IAM administration.
   - `HelpDesk` → `ReadOnlyAccess` to allow visibility into resources without edit/delete rights.
   - `Interns` → a custom scoped-down policy limited to specific training resources.
4. **Created individual IAM users** for each fictional employee and added them to the appropriate group — no permissions were attached directly to a user.
5. **Enabled MFA** on the admin-level account to reflect real-world security practice for privileged access.
6. **Set an account password policy** (minimum length, complexity requirements, rotation).
7. **Tested each role** by signing in as a representative user from each group and confirming they could only perform actions appropriate to that role (e.g., confirming the intern account was denied access to EC2).
8. **Documented the structure** with a diagram and this write-up so the design is easy to review and reproduce.

---

# Challenges / What I'd Do Differently

- Writing a custom least-privilege policy for the Interns group took more trial and error than using managed policies — initial versions were either too permissive or blocked legitimate training actions, which reinforced why testing each role matters.
- Next time, I'd set up IAM Access Analyzer from the start to catch overly broad permissions earlier instead of catching them through manual testing.
