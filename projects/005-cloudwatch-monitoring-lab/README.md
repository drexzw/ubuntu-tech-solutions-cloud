# Ubuntu Tech Solutions — CloudWatch Monitoring Lab

## Overview

This project simulates a Cloud/IT support monitoring task for Ubuntu Tech Solutions.

The objective is to configure Amazon CloudWatch monitoring for an Ubuntu EC2 instance and create an alerting workflow that can notify IT staff when server CPU utilization becomes unusually high.

The lab focuses on basic cloud monitoring, alerting, troubleshooting, and incident documentation.

## Scenario

Ubuntu Tech Solutions has an Ubuntu EC2 server that hosts a web application. The IT team wants basic monitoring in place so that resource problems can be detected before they affect users.

As a Junior IT / Cloud Support Technician, the task is to:

* Monitor the Ubuntu EC2 instance.
* Review CPU utilization through Amazon CloudWatch.
* Create a CloudWatch dashboard.
* Create a CPU utilization alarm.
* Configure Amazon SNS for notifications.
* Generate CPU load to test the monitoring system.
* Verify that the alarm triggers and recovers.
* Document the incident and resolution.

## Objectives

* Understand how EC2 metrics are collected by CloudWatch.
* Monitor CPU utilization for an EC2 instance.
* Create a CloudWatch dashboard.
* Configure a CloudWatch alarm.
* Use Amazon SNS for alert notifications.
* Simulate a high-CPU incident.
* Investigate and verify the server's condition.
* Document the incident using an IT support workflow.

## AWS Services Used

* Amazon EC2
* Amazon CloudWatch
* Amazon SNS

## Operating System

* Ubuntu Linux

## Monitoring Metric

### CPUUtilization

The primary metric used in this lab is `CPUUtilization`.

This metric represents the percentage of allocated CPU capacity being used by the EC2 instance.

A high CPU utilization value may indicate:

* Heavy application workload
* Resource-intensive processes
* A runaway process
* Insufficient instance capacity
* Unexpected activity

For this lab, a threshold of 70% is used to make testing the alarm easier.

## Monitoring Architecture

```text
Ubuntu EC2 Instance
        |
        | CPU Metrics
        v
Amazon CloudWatch
        |
        +------------------+
        |                  |
        v                  v
CloudWatch Dashboard   CloudWatch Alarm
                           |
                           | Alarm Trigger
                           v
                         Amazon SNS
                           |
                           v
                       Email Alert
```

## Current Progress

The Ubuntu EC2 instance was successfully located in the EC2 console and identified as `ubuntu-web-server`.

The `CPUUtilization` metric was located under the EC2 per-instance metrics in CloudWatch.

CloudWatch successfully displayed CPU utilization data for the instance, confirming that the EC2 instance was publishing the expected metric.

The initial observed CPU utilization was approximately 3.9%, indicating relatively low CPU activity at the time of observation.

## Evidence

Screenshots collected for this project include:

1. `01-ec2-instance-running.png` — Ubuntu EC2 instance running.
2. `02-cloudwatch-cpu-metric.png` — CPU utilization metric in CloudWatch.
3. `03-cloudwatch-dashboard.png` — CloudWatch monitoring dashboard.
4. `04-cloudwatch-alarm-ok.png` — CPU alarm in the OK state.
5. `05-high-cpu-detected.png` — Increased CPU utilization during testing.
6. `06-cloudwatch-alarm-triggered.png` — CloudWatch alarm entering the ALARM state.
7. `07-sns-alert-email.png` — SNS email notification.
8. `08-cloudwatch-alarm-recovered.png` — Alarm returning to the OK state.

## Skills Practiced

* Amazon EC2 monitoring
* Amazon CloudWatch
* CloudWatch metrics
* CloudWatch dashboards
* CloudWatch alarms
* Amazon SNS
* Linux command-line administration
* CPU troubleshooting
* Incident response
* Technical documentation

## Real-World IT Application

In a real IT or Cloud Support environment, monitoring systems help technicians detect infrastructure problems before users report them.

A typical workflow may look like:

```text
Monitor
   ↓
Detect abnormal behavior
   ↓
Receive alert
   ↓
Investigate
   ↓
Identify cause
   ↓
Resolve
   ↓
Verify recovery
   ↓
Document
```

This project demonstrates that workflow using an Ubuntu EC2 server and AWS monitoring services.

## Cleanup

After completing the lab, unnecessary AWS resources should be removed or stopped to avoid unexpected charges.

Resources should only be terminated if they are no longer needed for other projects or labs.
