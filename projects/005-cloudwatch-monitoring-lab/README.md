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

## Current Progress — Complete

The Ubuntu EC2 instance was located in the EC2 console and confirmed running as `ubuntu-web-server` (`i-0183ac253ea656367`).

The `CPUUtilization` metric was located under EC2 per-instance metrics in CloudWatch, and an initial baseline of approximately 3.9% CPU utilization was observed.

A CloudWatch dashboard (`Ubuntu-EC2-High-CPU`) was created showing CPUUtilization, NetworkIn, NetworkOut, and StatusCheckFailed for the instance.

A CloudWatch alarm (`Ubuntu-EC2-High-CPU`) was created with a threshold of CPUUtilization > 70% for 1 datapoint within 5 minutes, and an Amazon SNS topic was configured and subscribed to an email address for notifications.

CPU load was generated on the instance using the `stress` utility. CloudWatch detected the increase, the alarm transitioned from OK to **ALARM** (peaking at 99.6% CPU), and an SNS email notification was received confirming the state change with full alarm details (name, threshold, timestamp, instance ID).

The stress test was stopped, CPU utilization dropped, and the alarm returned to the **OK** state, confirming recovery.

## Evidence

Screenshots collected for this project:

1. `01-ec2-instance-running.png` — Ubuntu EC2 instance (`ubuntu-web-server`) confirmed running in the EC2 console.
2. `02-cloudwatch-cpu-metric.png` — CPUUtilization metric graphed in CloudWatch, confirming the instance is publishing data.
3. `03-cloudwatch-dashboard.png` — CloudWatch dashboard showing CPUUtilization, NetworkIn, NetworkOut, and StatusCheckFailed.
4. `04-cloudwatch-alarm-ok.png` — The `Ubuntu-EC2-High-CPU` alarm prior to the stress test, in the **Insufficient Data** state (newly created alarms sit here until enough datapoints have been collected).
5. `05-high-cpu-detected.png` — CPUUtilization metric climbing after the stress test was started.
6. `06-cloudwatch-alarm-triggered.png` — The alarm entering the **ALARM** state as CPU utilization crossed the 70% threshold, peaking at 99.6%.
7. `07-sns-alert-email.png` — SNS email notification confirming the OK → ALARM state change, with full alarm and metric details.
8. `08-cloudwatch-alarm-recovered.png` — The alarm returning to the **OK** state after the stress test was stopped and CPU utilization dropped.

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
