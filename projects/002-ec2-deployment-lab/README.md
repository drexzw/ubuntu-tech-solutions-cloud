# Ubuntu Tech Solutions - EC2 Deployment Lab

## Overview

This project demonstrates the deployment and configuration of a Linux web server using Amazon Web Services (AWS) EC2.

As part of the Ubuntu Tech Solutions cloud environment, an Ubuntu Server instance was deployed, secured, accessed remotely through SSH, and configured with Apache Web Server to host a custom webpage.

This lab simulates a real-world task completed by a Junior Cloud Administrator or IT Support Technician responsible for maintaining cloud infrastructure.

---

## Business Problem

The Ubuntu Tech Solutions infrastructure team needed a Linux-based web server for internal testing and future cloud projects. The server had to be:

- Remotely accessible for administration
- Reachable over HTTP for web traffic
- Configured to host a basic company webpage
- Verified as fully operational before handoff

---

## Objectives

- Deploy an Ubuntu Linux server using Amazon EC2
- Configure AWS Security Groups for controlled network access
- Establish remote server access using SSH
- Install and configure Apache Web Server
- Deploy a custom HTML webpage
- Perform basic Linux server verification
- Document the deployment process

---

## Technologies Used

- Amazon Web Services (AWS)
- Amazon EC2
- Ubuntu Server 24.04 LTS
- Apache2 Web Server
- SSH
- Linux Command Line
- HTML
- AWS Security Groups

---

## Project Environment

| Detail | Value |
|---|---|
| Cloud Provider | Amazon Web Services |
| Service Used | Amazon Elastic Compute Cloud (EC2) |
| Operating System | Ubuntu Server 24.04 LTS |
| Instance Type | t2.micro |
| Web Server | Apache2 |
| Access Method | SSH using key-based authentication |

---

## Deployment Summary

### 1. EC2 Instance Deployment

An Ubuntu EC2 instance was launched with:

- Ubuntu Server 24.04 LTS AMI
- t2.micro instance type
- Public IP address enabled
- A new key pair generated for SSH access

### 2. Security Group Configuration

A Security Group was configured to control inbound traffic to the instance:

- **SSH (port 22)** — restricted to allow remote administration
- **HTTP (port 80)** — opened to allow public web traffic to reach the Apache server

Keeping these as the only two open inbound rules follows the principle of least privilege — no unnecessary ports were exposed.

### 3. Remote Access via SSH

The instance was accessed remotely using key-based SSH authentication:

```bash
ssh -i key-name.pem ubuntu@public-ip-address
```

Key-based auth was used instead of password login, consistent with standard cloud server security practice.

### 4. System Updates

Before installing any software, the package index was updated and existing packages upgraded:

```bash
sudo apt update
sudo apt upgrade -y
```

### 5. Apache Web Server Installation

Apache was installed and its status verified:

```bash
sudo apt install apache2 -y
sudo systemctl status apache2
```

Apache was also enabled to start automatically on boot:

```bash
sudo systemctl enable apache2
```

### 6. Custom Webpage Deployment

The default Apache page was replaced with a custom HTML webpage:

```bash
cd /var/www/html
sudo nano index.html
```

### 7. Verification

The deployment was verified from both the server side and the browser side:

| Test | Result |
|---|---|
| SSH Connection | ✅ Successful |
| Apache Service Status | ✅ Running |
| HTTP Access (port 80) | ✅ Successful |
| Website Loading in Browser | ✅ Successful |

Additional system checks were run to confirm the server was healthy:

```bash
df -h        # disk usage
free -h      # memory usage
uptime       # server uptime
ss -tulnp    # confirm Apache listening on port 80
```

Evidence: [Screenshots](./screenshots/)

---

## Challenges / What I'd Do Differently

- Getting HTTP access working required double-checking that the Security Group, not just the OS firewall, allowed inbound traffic on port 80 — a reminder that AWS network-level rules and the instance's local firewall are two separate layers that both need to be correct.
- Next time, I'd script the update/install/verification steps (`apt update && apt upgrade && apt install apache2`, service checks) into a single reusable shell script rather than running each command manually, to make the deployment repeatable and closer to how this would be handled in a real environment.
- I'd also add basic hardening (disabling password auth entirely in `sshd_config`, restricting SSH to a specific IP range) as a follow-up step, since this instance was left in the network-restricted-but-otherwise-default state.

---

## Result

The EC2 instance was successfully deployed, secured at the network level, and configured to serve a custom webpage over HTTP. The server is fully operational and ready for future cloud administration exercises such as monitoring, security hardening, and automation.

---

## Documentation

- [Commands](./commands.md)
- [Support Ticket](./support-ticket.md)
- [Screenshots](./screenshots/)
