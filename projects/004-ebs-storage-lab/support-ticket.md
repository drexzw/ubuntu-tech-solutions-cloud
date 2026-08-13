# Support Ticket — EBS Storage Configuration

## Ticket Information

**Ticket ID:** AWS-EBS-001

**Priority:** Medium

**Category:** Cloud Infrastructure / Storage

**Status:** Resolved

**Environment:** AWS EC2 / Ubuntu Linux

## Issue

An EC2 Linux server requires additional persistent storage for application data.

The requested EBS volume was created successfully, but the volume was not available for attachment to the target EC2 instance.

## User Impact

The server does not currently have the additional storage required for the application workload.

This could prevent the application from storing additional data.

## Initial Investigation

The EC2 instance and EBS volume were inspected in the AWS Management Console.

The following configuration was identified:

**EC2 Instance**

```text
Region: us-east-2
Availability Zone: us-east-2c
```

**EBS Volume**

```text
Region: us-east-2
Availability Zone: us-east-2a
```

## Root Cause

The EBS volume and EC2 instance were located in different Availability Zones.

EBS volumes must be attached to EC2 instances within the same Availability Zone.

## Resolution

The original volume configuration was reviewed and a replacement EBS volume was created in the same Availability Zone as the EC2 instance:

```text
us-east-2c
```

The new volume was tagged for easier identification and attached to the EC2 instance.

The Linux system was then configured to use the new storage.

## Linux Configuration

The newly attached block device was identified using:

```bash
lsblk
```

The volume was formatted using the `ext4` filesystem and mounted at:

```text
/mnt/ebs-data
```

The mount was verified with:

```bash
df -h
```

Persistent mounting was configured through:

```text
/etc/fstab
```

The configuration was tested with:

```bash
sudo mount -a
```

## Validation

The following checks were completed:

* [x] EBS volume created
* [x] Correct Availability Zone confirmed
* [x] Volume tagged
* [x] Volume attached to EC2
* [x] Block device identified
* [x] Filesystem created
* [x] Mount point created
* [x] Volume mounted
* [x] Storage verified with `df -h`
* [x] `/etc/fstab` configured
* [x] Persistent mounting tested

## Resolution Summary

The storage issue was caused by an Availability Zone mismatch.

The EBS volume was recreated in `us-east-2c`, attached to the EC2 instance, configured within Ubuntu, and set up for persistent mounting.

**Final Status:** Resolved
