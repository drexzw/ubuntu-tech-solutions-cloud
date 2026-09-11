# Troubleshooting

This document records troubleshooting performed during the AWS Auto Scaling lab.

The purpose of documenting these issues is to show the actual diagnostic process used during deployment rather than only documenting the final successful configuration.

---

## Issue 1: Incorrect AMI Selected in the Launch Template

### Symptom

Instances launched by the Auto Scaling Group were not behaving as expected when Apache was checked.

When attempting to check the Apache service, the following type of error was encountered:

```text
apache2.service could not be found
```

### Investigation

The existing Ubuntu Tech Solutions web servers were running Ubuntu.

The Auto Scaling Group, however, had initially been configured using a Launch Template based on an Amazon Linux AMI.

This meant the newly launched instances were running a different operating system from the original web servers.

The Apache service/package configuration differs between Ubuntu and Amazon Linux.

### Resolution

A new version of the Launch Template was created using the correct Ubuntu AMI.

The Auto Scaling Group was then updated to use the corrected Launch Template version.

The incorrectly provisioned instances were terminated so that the Auto Scaling Group could launch replacement instances using the correct configuration.

**Evidence:** Not pictured. Screenshot 01 (`01-launch-template-created.png`) shows only the final, corrected Ubuntu launch template — the incorrect Amazon Linux version and the `apache2.service could not be found` error were not captured.

### Lesson

The AMI is a critical part of an EC2 Launch Template.

When an Auto Scaling Group is responsible for automatically creating servers, the operating system and configuration in the Launch Template must match the requirements of the application.

---

## Issue 2: Incorrect Instances Were Initially Present

### Symptom

The Target Group temporarily contained more instances than expected.

Some instances were the original web servers from the ALB lab, while others had been created by the Auto Scaling Group.

This made it difficult to immediately determine which instances were manually created and which were ASG-managed.

### Investigation

The EC2 instance launch times, Auto Scaling Group instance list, and Target Group registration were compared.

It was determined that the original ALB lab instances were still present while the Auto Scaling Group was also launching and managing its own instances.

### Resolution

The ASG-managed instances were identified through the Auto Scaling Group's Instance Management section.

Incorrect instances were terminated when necessary, while the original healthy ALB lab servers were left untouched until the ASG configuration was confirmed.

**Evidence:** `04-asg-instance-detaching-terminating.png`, `05-target-group-draining-scaling-event.png`, `06-asg-updating-capacity-4-instances.png`, and `07-target-group-healthy-post-scaling.png` capture this cleanup. Note that despite their filenames, 06 and 07 do not show a scale-to-maximum-capacity test — Desired capacity remains 2 throughout; the extra instances visible are the original ALB-lab servers being reconciled out, not a deliberate scale-out event.

### Lesson

When troubleshooting Auto Scaling environments, the EC2 console alone is not enough to determine ownership.

The Auto Scaling Group's Instance Management page should be used to identify instances controlled by the ASG.

---

## Issue 3: Target Group Temporarily Reported an Instance as Unhealthy

### Symptom

A newly launched instance temporarily appeared as unhealthy in the Target Group.

At the same time, Apache was confirmed to be running correctly on the instance.

### Investigation

The discrepancy indicated that the problem might not be Apache itself.

An EC2 instance being operational does not automatically mean that the Application Load Balancer health check has completed successfully.

The Target Group health-check process can take time after a new instance is launched or registered.

### Resolution

The instance was allowed additional time to complete its health checks.

The Target Group was monitored while AWS completed the health-check process.

The instance eventually changed to a healthy state.

### Lesson

A temporary `Unhealthy` state does not necessarily mean the web server is broken.

When troubleshooting ALB health checks, it is important to distinguish between:

```text
EC2 instance is running
```

and:

```text
ALB health check has successfully passed
```

Both need to be verified independently.

---

## Issue 4: Instance Entered Draining State

### Symptom

An instance appeared as `Draining` in the Target Group after it was terminated.

### Explanation

When a target is being removed, the Application Load Balancer can stop sending new connections to the instance while allowing existing connections to finish.

Therefore, the instance may remain visible in the Target Group temporarily instead of disappearing immediately.

### Resolution

No manual intervention was required.

The instance was allowed to complete the deregistration process.

### Lesson

AWS infrastructure state changes are not always immediate.

States such as `Draining`, `Terminating`, and `Pending` can occur during normal lifecycle operations.

---

## Issue 5: Auto Scaling Replacement Behavior

### Symptom

An ASG-managed instance was accidentally terminated during testing.

Initially, the expected replacement instance was not immediately visible.

### Investigation

The Auto Scaling Group's Instance Management and Activity History were checked.

The Target Group was also monitored for changes in registered targets.

### Resolution

The environment was allowed time to reconcile the desired capacity.

AWS subsequently launched replacement capacity and the new instance went through the normal boot, Apache configuration, target registration, and health-check process.

### Lesson

Auto Scaling operates asynchronously.

After terminating an ASG-managed instance, it may take time for the following sequence to complete:

```text
Instance termination
        ↓
ASG detects reduced capacity
        ↓
Replacement instance launched
        ↓
Operating system boots
        ↓
User Data executes
        ↓
Apache starts
        ↓
Target registered
        ↓
ALB health check
        ↓
Target becomes healthy
```

---

## Troubleshooting Approach

The main troubleshooting approach used in this lab was:

1. Identify the exact symptom.
2. Check the EC2 instance itself.
3. Verify the operating system and server configuration.
4. Check the Auto Scaling Group configuration.
5. Check the Target Group.
6. Allow time for AWS health checks and lifecycle transitions.
7. Verify the final state rather than relying on a single intermediate status.

This helped distinguish actual configuration problems from normal delays in AWS resource state changes.
