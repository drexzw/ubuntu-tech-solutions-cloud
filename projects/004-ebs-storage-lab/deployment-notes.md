# EBS Storage Deployment Notes

## Deployment Summary

This deployment adds persistent block storage to an Ubuntu EC2 instance using Amazon Elastic Block Store (EBS).

The storage was provisioned separately from the EC2 instance, attached to the instance, configured within Linux, and added to `/etc/fstab` for persistent mounting.

## AWS Configuration

**Region:**

```text
us-east-2
```

**EC2 Availability Zone:**

```text
us-east-2c
```

**EBS Availability Zone:**

```text
us-east-2c
```

**Filesystem:**

```text
ext4
```

**Mount Point:**

```text
/mnt/ebs-data
```

## Deployment Process

### 1. EC2 Instance

An Ubuntu EC2 instance was launched and confirmed to be running.

The instance was located in:

```text
us-east-2c
```

### 2. EBS Volume

An EBS volume was created for additional storage.

During the initial deployment, the volume was accidentally created in:

```text
us-east-2a
```

The target EC2 instance was in:

```text
us-east-2c
```

The Availability Zone mismatch prevented the volume from being attached.

### 3. Corrected EBS Volume

A replacement volume was created in:

```text
us-east-2c
```

The volume was tagged to make it easier to identify within the AWS console.

### 4. Volume Attachment

The EBS volume was attached to the Ubuntu EC2 instance.

AWS displayed the volume as:

```text
In-use
```

after successful attachment.

### 5. Linux Detection

The EC2 instance was accessed through SSH.

The attached storage was identified using:

```bash
lsblk
```

### 6. Filesystem Configuration

The new volume was formatted with:

```text
ext4
```

A mount point was created:

```text
/mnt/ebs-data
```

The volume was mounted and verified.

### 7. Persistent Mount

The filesystem UUID was retrieved and added to:

```text
/etc/fstab
```

The configuration was tested with:

```bash
sudo mount -a
```

The mounted filesystem was then verified using:

```bash
df -h
```

## Deployment Validation

| Test                     | Expected Result                  | Status |
| ------------------------ | -------------------------------- | ------ |
| EC2 instance running     | Instance state = Running         | Passed |
| Correct AZ               | EC2 and EBS both in `us-east-2c` | Passed |
| EBS attachment           | Volume state = In-use            | Passed |
| Linux detection          | Device appears in `lsblk`        | Passed |
| Filesystem               | `ext4` detected                  | Passed |
| Mount                    | `/mnt/ebs-data` available        | Passed |
| Disk verification        | Volume appears in `df -h`        | Passed |
| Persistent configuration | UUID present in `/etc/fstab`     | Passed |
| `mount -a` test          | No mount errors                  | Passed |

## Troubleshooting Performed

### Problem

The EBS volume was not available for attachment.

### Investigation

The Availability Zones of the EC2 instance and EBS volume were compared.

### Finding

```text
EC2: us-east-2c
EBS: us-east-2a
```

### Resolution

The EBS volume was recreated in:

```text
us-east-2c
```

The new volume was successfully attached.

## Operational Considerations

* EBS volumes should be created in the same Availability Zone as the EC2 instance that will use them.
* Device names should be confirmed with `lsblk` before performing filesystem operations.
* UUIDs should be used in `/etc/fstab` for persistent mounts.
* `/etc/fstab` should be backed up before modification.
* `nofail` can be used to prevent a boot failure if the EBS volume is temporarily unavailable.
* EBS volumes should be deleted when no longer required to avoid unnecessary AWS charges.

## Evidence

Deployment screenshots are stored in the `screenshots/` directory.

The evidence demonstrates the AWS configuration, troubleshooting process, volume attachment, Linux storage configuration, and persistent mount verification.

## Final State

The Ubuntu EC2 instance has an additional EBS volume attached and mounted at:

```text
/mnt/ebs-data
```

The volume is configured for persistent mounting through `/etc/fstab`.

**Deployment Status:** Completed
