# Ubuntu Tech Solutions — Highly Available Web Application

## Overview

This project demonstrates the deployment of a highly available web application for a fictional business, Ubuntu Tech Solutions.

The original environment consisted of individual web servers. To improve availability and resilience, two Ubuntu web servers were deployed across separate Availability Zones and placed behind an AWS Application Load Balancer.

The project demonstrates AWS networking, compute, security groups, health checks, traffic distribution, and failure recovery.

## Business Objective

Ubuntu Tech Solutions wants its web application to remain available if one web server becomes unavailable.

The solution should:

* Distribute incoming HTTP traffic across multiple web servers
* Detect unhealthy web servers automatically
* Stop routing traffic to failed servers
* Continue serving users through healthy servers
* Support recovery when a failed server becomes healthy again

## Architecture

```text
                         Internet
                            |
                            v
                 +---------------------+
                 | Application Load    |
                 | Balancer             |
                 | ubuntu-tech-alb      |
                 +----------+----------+
                            |
                    HTTP / Port 80
                            |
              +-------------+-------------+
              |                           |
              v                           v
     +------------------+        +------------------+
     | Web Server 01    |        | Web Server 02    |
     | Ubuntu + Apache  |        | Ubuntu + Apache  |
     | Public Subnet A  |        | Public Subnet B  |
     | 10.0.0.0/24      |        | 10.0.1.0/24      |
     +------------------+        +------------------+
              |                           |
             AZ-A                        AZ-B
```

## AWS Resources

### Networking

* VPC: `ubuntu-tech-vpc`
* VPC CIDR: `10.0.0.0/16`
* Internet Gateway: `ubuntu-tech-igw`
* Public Subnet A: `10.0.0.0/24` (`ubuntu-tech-public-subnet-a`)
* Public Subnet B: `10.0.1.0/24` (`ubuntu-tech-public-b`)
* Public Route Table: `ubuntu-tech-public-rt`
* Internet route: `0.0.0.0/0 → Internet Gateway`

### Compute

* `ubuntu-tech-web-01`
* `ubuntu-tech-web-web-2` *(naming inconsistency — this instance was intended to be named `ubuntu-tech-web-02`; the console shows `ubuntu-tech-web-web-2`. Left as-is here to match the actual resource rather than the intended name.)*
* Ubuntu Server 26.04 LTS
* Instance type: `t3.micro`
* Apache HTTP Server

### Load Balancing

* Application Load Balancer: `ubuntu-tech-alb`
* Target Group: `ubuntu-tech-web-tg`
* Protocol: HTTP
* Port: 80
* Health check path: `/` *(not pictured — no screenshot captures the target group's Health Checks tab; this reflects the configured value, not a screenshotted one)*

### Security

* ALB security group: `ubuntu-tech-alb-sg`
* Web server security group: `ubuntu-tech-web-sg`

The ALB accepts public HTTP traffic, while the web servers accept HTTP traffic from the ALB security group.

This creates a basic security boundary between the public entry point and the backend servers.

> **Note on evidence:** the screenshot of `ubuntu-tech-alb-sg` (taken immediately after creation) shows **0 inbound rules**. Since the ALB was reachable over HTTP in later tests, an inbound rule for `HTTP:80` from `0.0.0.0/0` must have been added afterward, but no screenshot documents that step. Treat the "ALB SG permits HTTP from 0.0.0.0/0" claim below as **not pictured**.

## Implementation

### 1. Network Foundation

A VPC using `10.0.0.0/16` was created with two public subnets located in separate Availability Zones.

The public route table contains:

```text
0.0.0.0/0 → Internet Gateway
```

This provides internet connectivity for the lab's public resources.

### 2. Web Servers

Two Ubuntu EC2 instances were deployed:

```text
Web Server 01 → 10.0.0.0/24 → AZ-A
Web Server 02 → 10.0.1.0/24 → AZ-B
```

Apache was installed on both servers.

Each server was given a unique webpage so that the backend serving a request could be identified.

Example:

```text
Ubuntu Tech Solutions - Web Server 01
```

and:

```text
Ubuntu Tech Solutions - Web Server 02
```

### 3. Target Group

The two EC2 instances were registered with:

```text
ubuntu-tech-web-tg
```

The target group uses:

```text
HTTP
Port 80
Health check path /
```

*(Health check path confirmed via configuration, not directly screenshotted — see note above.)*

The ALB uses these health checks to determine which servers are capable of handling requests.

### 4. Application Load Balancer

An internet-facing Application Load Balancer was created across the two public subnets.

The listener accepts:

```text
HTTP : 80
```

and forwards requests to:

```text
ubuntu-tech-web-tg
```

### 5. Security Groups

The ALB security group is intended to permit:

```text
HTTP : 80
Source: 0.0.0.0/0
```

*(Not directly pictured — see evidence note above.)*

The web server security group permits:

```text
SSH : 22
Source: My IP

HTTP : 80
Source: ubuntu-tech-alb-sg
```

This prevents the backend HTTP service from being openly accessible to the entire internet.

## High Availability Test

The system was tested by intentionally stopping Apache on Web Server 01:

```bash
sudo systemctl stop apache2
```

The ALB health check subsequently detected that Web Server 01 was unhealthy.

The target group then showed:

```text
Web Server 01 → Unhealthy
Web Server 02 → Healthy
```

The ALB continued serving the application through Web Server 02.

This demonstrated that a failure of one backend server did not make the application completely unavailable.

Apache was then restored:

```bash
sudo systemctl start apache2
```

After successful health checks, Web Server 01 returned to a healthy state.

## Result

The final environment provides basic application-level high availability using:

* Multiple EC2 instances
* Multiple Availability Zones
* Application Load Balancing
* Target-group health checks
* Security-group segmentation

The project demonstrates how AWS infrastructure can be designed to tolerate an individual web-server failure while maintaining application availability.

## Skills Demonstrated

* AWS VPC
* IPv4 subnetting
* Availability Zones
* Internet Gateway
* Route tables
* EC2
* Ubuntu Server
* Apache
* Security Groups
* Application Load Balancer
* Target Groups
* Health Checks
* High Availability
* Failure Testing
* Troubleshooting
* Linux system administration
* Basic cloud architecture

## Evidence

Screenshots documenting the deployment and testing are stored in the `screenshots/` directory.

Key evidence includes:

1. Multi-AZ subnet configuration
2. EC2 instances
3. Individual web-server pages
4. Security-group configuration
5. Healthy target group
6. ALB application access
7. Unhealthy target during failure testing
8. Application availability during failure
9. Target recovery

**Not pictured:**

* The ALB security group's inbound HTTP rule (only the pre-rule, 0-inbound-rule state was captured)
* The target group's Health Checks tab (path `/` reflects configuration, not a screenshot)

## Changelog

* Corrected Public Subnet B CIDR from `10.0.2.0/24` to `10.0.1.0/24` to match the actual subnet and instance evidence.
* Corrected Ubuntu version from `24.04 LTS` to `26.04 LTS` to match the SSH session screenshot.
* Flagged the `ubuntu-tech-web-02` / `ubuntu-tech-web-web-2` naming mismatch between the documentation and the actual AWS console.
* Labeled the ALB security group's public HTTP rule and the target group's health-check path as **not pictured**, since no screenshot directly evidences either.
