# Business Scenario — Ubuntu Tech Solutions

## Company Background

Ubuntu Tech Solutions is a growing technology company that operates a web application used by customers and internal staff.

As the company grows, the application needs to remain available even when individual web servers experience failures or require maintenance.

The company also needs the ability to increase server capacity without manually configuring every new server.

## The Problem

Initially, the web application relied on manually managed EC2 instances behind an Application Load Balancer.

While this architecture provides load balancing, manually managing the EC2 fleet creates operational challenges.

If one server fails, an administrator must manually identify the problem and replace the server.

If traffic increases, an administrator must manually launch and configure another EC2 instance.

This creates several risks:

* Longer recovery times after server failures.
* Inconsistent server configurations.
* Increased administrative workload.
* Difficulty responding quickly to changes in demand.

## Proposed Solution

Ubuntu Tech Solutions can use an Amazon EC2 Auto Scaling Group together with an Application Load Balancer.

The Auto Scaling Group maintains a minimum number of web servers and automatically launches replacement instances when required.

The Launch Template provides the configuration used to create new servers, including the Ubuntu operating system, instance type, security group, and automated Apache installation.

The Application Load Balancer distributes incoming traffic only to healthy registered instances.

## Expected Architecture

```text
                         Customers
                             |
                             v
                    Application Load
                       Balancer
                             |
                      Target Group
                             |
                 +-----------+-----------+
                 |                       |
                 v                       v
           Web Server A            Web Server B
                 ^                       ^
                 |                       |
                 +-----------+-----------+
                             |
                    Auto Scaling Group
                             |
                    Launch Template
```

## Failure Scenario

Suppose one of the web servers becomes unavailable.

Without Auto Scaling:

```text
Server failure
     ↓
Application capacity decreases
     ↓
Administrator investigates
     ↓
Administrator launches/configures replacement
```

With Auto Scaling:

```text
Server failure
     ↓
ASG detects reduced capacity
     ↓
Replacement instance launched
     ↓
User Data configures server
     ↓
ALB health check passes
     ↓
Replacement receives traffic
```

This reduces the amount of manual intervention required from the IT team.

## Scaling Scenario

If application demand increases, the Auto Scaling Group can increase the number of EC2 instances within its configured limits.

For this lab:

```text
Minimum capacity: 2
Desired capacity: 2
Maximum capacity: 4
```

This allows the environment to maintain a baseline of two servers while providing room for additional capacity.

## Business Benefits

### Improved Availability

Multiple web servers reduce dependence on a single instance.

### Automated Recovery

Failed ASG-managed instances can be automatically replaced.

### Easier Scaling

Additional instances can be launched without manually configuring every server.

### Consistent Configuration

New instances are created from the same Launch Template and User Data configuration.

### Reduced Administrative Work

The IT team spends less time manually provisioning and replacing web servers.

## Future Improvements

A production implementation could extend this architecture with:

* CloudWatch alarms and monitoring.
* More sophisticated scaling policies.
* HTTPS using AWS Certificate Manager.
* Route 53 DNS.
* Private subnets and NAT Gateway architecture.
* Centralized logging.
* Infrastructure as Code using Terraform or AWS CloudFormation.
* Application-level health checks.
* CI/CD deployment automation.

## Conclusion

The Auto Scaling architecture provides Ubuntu Tech Solutions with a more resilient and manageable web application environment.

By combining an Application Load Balancer with an Auto Scaling Group and automated EC2 configuration, the company can maintain application availability while reducing the manual effort required to manage its web server fleet.
