# Ubuntu Tech Solutions — Support Ticket

## Ticket Information

**Ticket ID:** UTS-2026-004
**Priority:** Medium
**Category:** Cloud Infrastructure / Monitoring
**Status:** In Progress
**Assigned Role:** Junior IT / Cloud Support Technician

---

## Issue

The Ubuntu Tech Solutions application server has been reported as occasionally becoming slow.

The IT team requested monitoring and alerting for the Ubuntu EC2 server so that unusually high resource utilization can be detected before it causes a significant service impact.

---

## Initial Investigation

The Ubuntu EC2 instance was located in the Amazon EC2 console.

**Instance:** `ubuntu-web-server`

The instance was confirmed to be running.

Amazon CloudWatch was then opened to review the available EC2 metrics.

The `CPUUtilization` metric was located under the EC2 per-instance metrics.

CloudWatch successfully displayed CPU utilization data for the instance.

The initial observed CPU utilization was approximately **3.9%**, indicating relatively low CPU activity at the time of investigation.

---

## Monitoring Plan

The following monitoring solution is being configured:

1. Monitor EC2 CPU utilization using CloudWatch.
2. Create a CloudWatch dashboard.
3. Create a CPU utilization alarm.
4. Configure an Amazon SNS notification topic.
5. Subscribe an email address to the SNS topic.
6. Generate artificial CPU load for testing.
7. Verify that the alarm changes from `OK` to `ALARM`.
8. Confirm receipt of the SNS email notification.
9. Stop the CPU workload.
10. Verify that the alarm returns to `OK`.

---

## Investigation Commands

The following Linux commands are used during the investigation and testing process:

```bash
top
```

Used to monitor CPU utilization and running processes.

```bash
sudo apt update
```

Used to update Ubuntu package information.

```bash
sudo apt install stress -y
```

Used to install the CPU stress-testing utility.

```bash
stress --cpu 2 --timeout 300
```

Used to generate artificial CPU workload for monitoring validation.

---

## Expected Resolution

The monitoring system should detect when CPU utilization exceeds the configured threshold.

The expected workflow is:

```text
High CPU Usage
      ↓
CloudWatch Metric
      ↓
CloudWatch Alarm
      ↓
ALARM State
      ↓
SNS Notification
      ↓
IT Technician Investigation
      ↓
CPU Load Removed
      ↓
Alarm Returns to OK
```

---

## Current Status

**In Progress**

The EC2 instance has been confirmed as running, and CloudWatch is successfully receiving the `CPUUtilization` metric.

The remaining work is to complete the dashboard, alarm, SNS notification, CPU stress test, and recovery verification.

---

## Resolution Notes

To be completed after the CloudWatch alarm and SNS notification have been successfully tested.

The final resolution will document:

* Cause of the simulated high CPU condition
* CloudWatch alarm response
* SNS notification
* Investigation performed
* Corrective action
* Recovery confirmation

---

## Lessons Learned

This lab demonstrates a basic cloud monitoring and incident-response workflow.

The main concepts practiced are:

* EC2 resource monitoring
* CloudWatch metrics
* CloudWatch dashboards
* CloudWatch alarms
* SNS notifications
* Linux CPU investigation
* Incident response
* Recovery verification
* Technical documentation
