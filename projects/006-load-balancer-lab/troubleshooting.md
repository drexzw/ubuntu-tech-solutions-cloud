# Ubuntu Tech Solutions — Load Balancer Troubleshooting

## Problem: Target Is Unhealthy

### Symptoms

An EC2 instance appears as `Unhealthy` in the target group.

### Investigation

Check Apache:

```bash
sudo systemctl status apache2
```

Test the web server locally:

```bash
curl http://localhost
```

If Apache is stopped:

```bash
sudo systemctl start apache2
```

### Security Group Check

Verify that `ubuntu-tech-web-sg` allows:

```text
HTTP
Port 80
Source: ubuntu-tech-alb-sg
```

The web server should not require an HTTP rule from `0.0.0.0/0` once the ALB is being used as the public entry point.

### Health Check Check

Verify the target group uses:

```text
Protocol: HTTP
Port: 80
Path: /
```

The root webpage must return successfully.

---

## Problem: ALB Is Reachable but the Webpage Does Not Load

Check:

1. ALB state is `Active`
2. Listener uses HTTP port 80
3. Listener forwards to `ubuntu-tech-web-tg`
4. Target group contains both EC2 instances
5. At least one target is healthy
6. ALB security group permits HTTP port 80
7. Web-server security group permits HTTP from the ALB security group

---

## Problem: Both Targets Become Unhealthy

Check the backend servers first.

On each server:

```bash
sudo systemctl status apache2
```

Then:

```bash
curl http://localhost
```

If Apache responds locally, investigate the security group and target-group configuration.

This isolates the problem in stages:

```text
EC2
 ↓
Apache
 ↓
Security Group
 ↓
Target Group
 ↓
ALB
```

---

## Failure Test: Server 01 Becomes Unhealthy

For the intentional failure test, Apache was stopped:

```bash
sudo systemctl stop apache2
```

The ALB health check failed because the server could no longer respond successfully on HTTP port 80.

The expected target state was:

```text
Web Server 01 → Unhealthy
Web Server 02 → Healthy
```

The ALB continued forwarding traffic to the healthy target.

---

## Recovery

Apache was restored:

```bash
sudo systemctl start apache2
```

After subsequent successful health checks, Web Server 01 returned to:

```text
Healthy
```

The final target group state was:

```text
Web Server 01 → Healthy
Web Server 02 → Healthy
```

## Key Lesson

A load balancer does not simply distribute traffic between servers.

It uses health checks to determine which servers are capable of receiving traffic.

A backend failure therefore does not necessarily become an application outage when healthy targets remain available.
