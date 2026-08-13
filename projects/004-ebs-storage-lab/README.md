# EBS Storage Management Lab

## Overview

This lab demonstrates how to create, configure, attach, format, mount, and persist an Amazon Elastic Block Store (EBS) volume on an Amazon EC2 Linux instance.

The lab also includes troubleshooting an Availability Zone mismatch between the EC2 instance and the EBS volume.

## Scenario

A Linux server requires additional persistent storage for application data. The existing EC2 instance does not have sufficient storage available, so an additional EBS volume must be provisioned and mounted.

The storage must remain available after a server reboot.

## Objectives

* Create an EBS volume
* Configure the volume in the correct Availability Zone
* Apply resource tags
* Attach the EBS volume to an EC2 instance
* Identify the new block device from Linux
* Create a filesystem
* Create a mount point
* Mount the EBS volume
* Verify available storage
* Configure persistent mounting with `/etc/fstab`
* Verify the configuration
* Troubleshoot an Availability Zone mismatch

## Environment

| Component         | Configuration             |
| ----------------- | ------------------------- |
| Cloud Provider    | Amazon Web Services (AWS) |
| Service           | Amazon EC2                |
| Storage           | Amazon EBS                |
| Region            | `us-east-2`               |
| Availability Zone | `us-east-2c`              |
| Operating System  | Ubuntu Linux              |
| Access Method     | SSH                       |
| Filesystem        | ext4                      |
| Mount Point       | `/mnt/ebs-data`           |

## Architecture

```text
                 AWS
                  |
          ┌───────┴────────┐
          │   us-east-2    │
          │                │
          │  us-east-2c   │
          │                │
          │  ┌─────────┐   │
          │  │  EC2    │   │
          │  │ Ubuntu  │   │
          │  └────┬────┘   │
          │       │        │
          │       │ Attach │
          │       ▼        │
          │  ┌─────────┐   │
          │  │   EBS   │   │
          │  │ Volume  │   │
          │  └─────────┘   │
          └────────────────┘
```

## Troubleshooting

During the initial configuration, the EBS volume was created in:

```text
us-east-2a
```

while the EC2 instance was located in:

```text
us-east-2c
```

The volume could not be attached because an EBS volume must be in the same Availability Zone as the EC2 instance.

### Resolution

A new EBS volume was created in:

```text
us-east-2c
```

The volume could then be attached successfully to the EC2 instance.

## Linux Configuration

After attaching the volume, the Linux system was checked using:

```bash
lsblk
```

The new block device was identified and formatted with `ext4`.

A mount point was then created:

```text
/mnt/ebs-data
```

The volume was mounted and verified using:

```bash
df -h
```

Finally, `/etc/fstab` was configured so the volume would automatically mount after reboot.

## Verification

The following were verified:

* EBS volume state changed to `In-use`
* New block device appeared in Linux
* Filesystem was created successfully
* Mount point was accessible
* `df -h` displayed the mounted volume
* `/etc/fstab` contained the persistent mount configuration
* `mount -a` completed successfully
* The volume remained available after reboot

## Skills Demonstrated

* AWS EC2
* Amazon EBS
* AWS Availability Zones
* Cloud storage management
* Linux block-device management
* Filesystem creation
* Linux mounting
* `/etc/fstab`
* Persistent storage configuration
* Troubleshooting
* AWS resource tagging
* Basic cloud infrastructure administration

## Evidence

Screenshots documenting the lab are stored in the `screenshots/` directory.

The screenshots document the process from EC2 deployment and EBS creation through attachment, Linux configuration, mounting, and persistence testing.
