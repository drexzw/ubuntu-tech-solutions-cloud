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
