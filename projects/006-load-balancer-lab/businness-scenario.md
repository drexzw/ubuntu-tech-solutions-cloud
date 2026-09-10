# Ubuntu Tech Solutions — Business Scenario

## Business Background

Ubuntu Tech Solutions is a small technology company that provides IT services and online resources to its customers.

The company hosts a web application on AWS that customers and employees rely on to access company information and services.

Initially, the application was hosted on a single web server. While this setup was functional, it introduced a single point of failure.

If the web server or the Availability Zone hosting it experienced an outage, users could lose access to the application.

## Business Problem

The existing single-server architecture created several risks:

* A failure of the web server could make the application unavailable.
* Application traffic could not be distributed across multiple servers.
* There was no automated mechanism to detect an unhealthy web server.
* Maintenance or server failures could require application downtime.
* The environment did not provide sufficient redundancy for a business-facing application.

Ubuntu Tech Solutions wanted to improve application availability without significantly increasing the complexity of its infrastructure.

## Business Requirements

The company required a solution that would:

1. Support multiple web servers.
2. Distribute incoming HTTP traffic between healthy servers.
3. Detect when a web server becomes unavailable.
4. Automatically stop sending traffic to an unhealthy server.
5. Continue serving users when one web server fails.
6. Place web servers across separate Availability Zones.
7. Restrict direct HTTP access to backend servers where possible.
8. Provide a foundation that could be expanded as the business grows.

## Proposed Solution

The solution uses AWS networking, EC2, security groups, and an Application Load Balancer.

Two Ubuntu web servers were deployed across separate Availability Zones:

```text
Web Server 01
Public Subnet A
AZ-A

Web Server 02
Public Subnet B
AZ-B
```

Both servers run Apache and host the Ubuntu Tech Solutions web application.

An internet-facing Application Load Balancer was placed in front of the servers.

```text
                         Internet
                            |
                            v
                 Application Load
                     Balancer
                            |
                 +----------+----------+
                 |                     |
                 v                     v
          Web Server 01         Web Server 02
             AZ-A                    AZ-B
```

The Application Load Balancer forwards requests to a target group containing the two web servers.

## Health Monitoring

The target group uses an HTTP health check against the root path:

```text
HTTP : 80
Path: /
```

*(Not pictured — no screenshot captures the target group's Health Checks tab; this reflects the configured value rather than a screenshotted one.)*

The load balancer uses the results of these health checks to determine which servers are available to receive traffic.

If a server stops responding correctly, it is marked unhealthy and removed from normal traffic routing.

## Security Design

The public-facing Application Load Balancer accepts HTTP traffic from the internet. *(Not pictured — the only screenshot of the ALB's security group shows 0 inbound rules at creation time; a public HTTP rule was evidently added afterward, since the ALB was reachable in later tests, but that step was not screenshotted.)*

The backend web servers use a separate security group.

The web-server security group permits HTTP traffic from the ALB security group rather than allowing unrestricted HTTP access from the internet.

This creates a basic separation between the public entry point and the application servers.

SSH access remains restricted to the administrator's IP address for management purposes.

## High Availability Test

The solution was tested by intentionally stopping Apache on Web Server 01.

```bash
sudo systemctl stop apache2
```

The Application Load Balancer subsequently detected the failed health check and marked Web Server 01 as unhealthy.

At that point:

```text
Web Server 01 → Unhealthy
Web Server 02 → Healthy
```

The application remained accessible through the load balancer because Web Server 02 was still healthy.

Apache was then restored on Web Server 01:

```bash
sudo systemctl start apache2
```

After successful health checks, Web Server 01 returned to a healthy state and became available to the load balancer again.

## Business Outcome

The redesigned architecture removes the single-server dependency and provides a basic level of application resilience.

Ubuntu Tech Solutions can now tolerate the failure of one web server without completely interrupting access to the application.

The solution also establishes a foundation for future improvements such as:

* HTTPS/TLS
* Auto Scaling
* Amazon CloudWatch alerting
* Private backend subnets
* NAT Gateway architecture
* Infrastructure as Code
* Automated deployments
* Additional application servers

## Skills Demonstrated

This project demonstrates practical experience with:

* AWS VPC networking
* IPv4 subnet design
* Availability Zones
* Internet Gateways
* Route tables
* EC2
* Ubuntu Server
* Apache
* Security Groups
* Application Load Balancers
* Target Groups
* Health Checks
* High Availability
* Failure Testing
* Linux administration
* Cloud troubleshooting
* Basic cloud architecture

## Conclusion

The Ubuntu Tech Solutions environment was redesigned from a single-server web application into a multi-server architecture using an AWS Application Load Balancer.

The completed solution distributes traffic across multiple Ubuntu servers, monitors backend health, and continues serving users when an individual web server becomes unavailable.

This demonstrates how cloud infrastructure can be designed around business requirements rather than simply deploying individual AWS services.

## Changelog

* Labeled the health-check path (`/`) as **not pictured** — no screenshot of the Health Checks tab exists.
* Labeled the ALB's public HTTP inbound rule as **not pictured** — the only ALB-SG screenshot shows 0 inbound rules at creation, with no follow-up screenshot showing the rule added.
