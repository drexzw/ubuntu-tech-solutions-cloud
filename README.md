# Ubuntu Tech Solutions — Cloud Infrastructure Portfolio

## Overview

Ubuntu Tech Solutions is a simulated managed IT services company built to practice hands-on AWS cloud administration and IT support skills.

This repository documents the design, deployment, troubleshooting, and monitoring of the company's AWS environment — from identity management through compute, storage, and observability. Each project is written up as a real IT support engagement, complete with a business problem, the solution implemented, screenshot evidence, and (where applicable) a support ticket documenting the incident lifecycle.

## Company Profile

| | |
|---|---|
| **Industry** | Managed IT Services |
| **Cloud Provider** | Amazon Web Services |
| **Role** | Junior Cloud Administrator / IT Support Technician |
| **Current Stage** | Startup → Cloud Migration |

## Projects Completed

| # | Project | Focus | Status |
|---|---|---|---|
| 001 | [Employee Identity & Access Management](./projects/001-iam) | IAM users, groups, least-privilege policies | ✅ Complete |
| 002 | [EC2 Deployment Lab](./projects/002-ec2-deployment-lab) | Ubuntu EC2, SSH, Apache web server deployment | ✅ Complete |
| 003 | [S3 Static Website Hosting](./projects/003-s3-static-website-lab-hosting) | S3 bucket configuration, public access, static hosting | ✅ Complete |
| 004 | [EBS Storage Management Lab](./projects/004-ebs-storage-lab) | EBS volumes, Linux mounting, Availability Zone troubleshooting | ✅ Complete |
| 005 | [CloudWatch Monitoring Lab](./projects/005-cloudwatch-monitoring-lab) | CloudWatch alarms, SNS alerting, incident response | ✅ Complete |

Each project folder contains its own `README.md` with full write-up, a `commands.md` reference where Linux/CLI work was involved, screenshot evidence, and a `support-ticket.md` for projects framed as an IT support incident.

## Skills Demonstrated Across This Repository

| Category | Skills |
|---|---|
| **Identity & Access** | IAM users/groups/policies, least-privilege access, role separation, MFA |
| **Compute** | EC2 deployment, Security Groups, SSH key-based access, Apache web server |
| **Storage** | S3 static website hosting, bucket policies, EBS volume provisioning and mounting |
| **Monitoring & Alerting** | CloudWatch metrics/dashboards/alarms, SNS notifications, incident response |
| **Linux Administration** | Package management, filesystem/mount configuration (`/etc/fstab`), process/CPU troubleshooting (`top`, `stress`) |
| **Documentation** | Business-problem framing, IT support ticket workflow, root-cause and resolution write-ups |

## Architecture & Company Documentation

- [Company Structure](./docs/company-structure.md) — org chart and departmental breakdown for the fictional company
- [Architecture Diagram](./docs/architecture.md)
- [Diagrams](./diagrams/diagrams.md)

## Repository Structure

```text
ubuntu-tech-solutions-cloud/
├── docs/                                   # Company structure, architecture, security policy
├── diagrams/                               # Supporting diagrams
└── projects/
    ├── 001-iam/                            # IAM identity & access management
    ├── 002-ec2-deployment-lab/             # EC2 + Apache web server deployment
    ├── 003-s3-static-website-lab-hosting/  # S3 static website hosting
    ├── 004-ebs-storage-lab/                # EBS volume provisioning & mounting
    └── 005-cloudwatch-monitoring-lab/      # CloudWatch monitoring & SNS alerting
```

## About This Portfolio

This repository is part of a self-directed path from IT support into cloud engineering. Each lab is designed to mirror a task a Junior Cloud Administrator or Help Desk Technician might realistically be assigned — provisioning access, deploying and securing infrastructure, and building monitoring/alerting so issues are caught before they affect users — with documentation written the way it would be for a real team, not just a checklist of console clicks.



