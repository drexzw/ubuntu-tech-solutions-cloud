# AWS Auto Scaling Web Application Lab

## Overview

This project demonstrates how to deploy a highly available web application using an Application Load Balancer (ALB) and an Amazon EC2 Auto Scaling Group.

The environment was built for Ubuntu Tech Solutions, a fictional technology company that needs a web application capable of maintaining availability when individual web servers fail and automatically adding or replacing instances when required.

The lab builds on the previous Application Load Balancer environment by introducing an Auto Scaling Group to manage the web server fleet.

## Business Scenario

Ubuntu Tech Solutions operates a web application that should remain available even if an individual web server becomes unavailable.

A manually managed group of EC2 instances creates several problems:

* Individual servers must be configured manually.
* Failed servers require manual replacement.
* Increasing capacity requires manually launching additional instances.
* Maintaining consistent configurations across servers becomes more difficult as the environment grows.

To address these problems, the environment uses an EC2 Auto Scaling Group combined with an Application Load Balancer.

The Auto Scaling Group maintains the required number of web servers, while the Application Load Balancer distributes incoming HTTP traffic across healthy instances.

## Architecture

```text
                         Internet
                            |
                            v
                   +----------------+
                   |      ALB       |
                   +-------+--------+
                           |
                    Target Group
                           |
              +------------+------------+
              |                         |
              v                         v
        +-----------+             +-----------+
        | EC2 Web   |             | EC2 Web   |
        | Server    |             | Server    |
        +-----------+             +-----------+
              ^                         ^
              |                         |
              +-----------+-------------+
                          |
                  Auto Scaling Group
                          |
                  Launch Template
```

## AWS Services Used

* Amazon EC2
* EC2 Launch Templates
* EC2 Auto Scaling Groups
* Application Load Balancer
* Elastic Load Balancing Target Groups
* Amazon VPC
* Security Groups
* Ubuntu Server
* Apache HTTP Server

## Implementation

### 1. Launch Template

A Launch Template was created to provide a consistent configuration for instances launched by the Auto Scaling Group.

The template specifies:

* Ubuntu Server AMI
* `t3.micro` instance type
* EC2 key pair
* Web-server security group
* User Data configuration

The User Data script automatically installs and starts Apache when a new instance is launched.

### 2. Auto Scaling Group

The Auto Scaling Group was configured with:

| Setting          | Configuration                  |
| ---------------- | ------------------------------ |
| Minimum capacity | 2                              |
| Desired capacity | 2                              |
| Maximum capacity | 4                              |
| Health checks    | EC2 and Elastic Load Balancing |
| Load balancing   | Existing ALB target group      |

The group launches instances using the configured Launch Template.

### 3. Load Balancer Integration

The Auto Scaling Group was connected to the existing Application Load Balancer target group.

New instances launched by the ASG are automatically registered with the target group.

The ALB performs health checks to determine whether instances are capable of receiving traffic.

### 4. Web Server Configuration

Apache is installed automatically through EC2 User Data.

The configuration creates a simple Ubuntu Tech Solutions web page identifying the server as an Auto Scaling web server.

This allows the environment to demonstrate that newly launched instances can become functional web servers without requiring manual configuration.

## Validation

The completed environment was validated by checking:

* EC2 instances launched successfully through the Auto Scaling Group.
* Apache was running on the web servers.
* Instances registered with the ALB target group.
* Target group health checks eventually reported the instances as healthy.
* The ALB successfully served the web application.
* An ASG-managed instance was detached/terminated, and the Auto Scaling Group launched a replacement to return to desired capacity (screenshots 04–07).
* The replacement instance was created from the Ubuntu Launch Template shown in screenshot 01.

**Not pictured:** the environment was never screenshotted scaling out beyond the baseline of 2 instances. Screenshots 06–07 show the Auto Scaling Group and Target Group reconciling back down to the desired capacity of 2 (clearing duplicate instances left over from the ALB lab — see Issue 2 in `troubleshooting.md`), not a deliberate scale-to-maximum-capacity (4) test.

## Key Concepts Demonstrated

### High Availability

Maintaining multiple web servers reduces dependence on a single EC2 instance.

### Automatic Instance Replacement

If an ASG-managed instance is terminated, the Auto Scaling Group can automatically launch a replacement to maintain the desired capacity.

### Load Balancing

The Application Load Balancer distributes requests across registered healthy web servers.

### Infrastructure Automation

The Launch Template and User Data configuration allow new instances to be configured automatically.

### Health-Based Traffic Distribution

The ALB uses target health checks to determine which instances should receive application traffic.

## Lessons Learned

A major lesson from this lab was the importance of maintaining consistency between the operating system selected in the Launch Template and the commands used to configure the web server.

An incorrect Amazon Linux AMI was initially selected instead of the Ubuntu AMI used by the existing web servers. This caused troubleshooting confusion because Apache is commonly managed as `apache2` on Ubuntu, while Amazon Linux uses a different package/service configuration.

The Launch Template was corrected to use Ubuntu, and the Auto Scaling Group was updated accordingly.

**Not pictured:** the incorrect Amazon Linux configuration, the resulting `apache2.service could not be found` error, and the intermediate launch template version were not screenshotted. Screenshot 01 shows only the final, corrected Ubuntu launch template (a single "Version 1 (Default)"). See `troubleshooting.md`, Issue 1, for the full write-up of this diagnostic process.

Another lesson was that Auto Scaling and Target Group state changes are not always instantaneous. Instances may temporarily appear as unhealthy, draining, or terminating while AWS completes health checks and lifecycle operations.

## Conclusion

The completed environment demonstrates an automated and fault-tolerant web server architecture.

Instead of manually managing individual EC2 servers, Ubuntu Tech Solutions can use an Auto Scaling Group to maintain application capacity while an Application Load Balancer distributes traffic across healthy instances.

This provides a foundation for scaling the application as demand changes while reducing the operational impact of individual server failures.
