# EBS Storage — Linux Commands

This document contains the Linux commands used to configure and verify the EBS volume after attaching it to the EC2 instance.

> Replace `/dev/xvdf` with the actual device identified by `lsblk` if AWS/Linux presents a different device name.

## 1. Identify Block Devices

```bash
lsblk
```

Used to identify the newly attached EBS volume.

---

## 2. Check Disk Information

```bash
lsblk -f
```

Displays block devices, filesystems, UUIDs, and mount points.

---

## 3. Create the Filesystem

```bash
sudo mkfs -t ext4 /dev/xvdf
```

Creates an `ext4` filesystem on the EBS volume.

**Warning:** Formatting a volume destroys existing data on that device. Only run this on a new/empty volume.

---

## 4. Create the Mount Point

```bash
sudo mkdir -p /mnt/ebs-data
```

Creates the directory where the EBS volume will be mounted.

---

## 5. Mount the Volume

```bash
sudo mount /dev/xvdf /mnt/ebs-data
```

Mounts the EBS volume to the mount point.

---

## 6. Verify the Mount

```bash
df -h
```

Confirms that the EBS volume is mounted and displays its available storage.

---

## 7. Check the Mount

```bash
lsblk -f
```

Verifies the filesystem and mount point.

---

## 8. Retrieve the UUID

```bash
sudo blkid /dev/xvdf
```

Returns the UUID assigned to the filesystem.

Example:

```text
/dev/xvdf: UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" TYPE="ext4"
```

---

## 9. Back Up `/etc/fstab`

```bash
sudo cp /etc/fstab /etc/fstab.backup
```

Creates a backup before modifying the filesystem table.

---

## 10. Edit `/etc/fstab`

```bash
sudo nano /etc/fstab
```

Add an entry using the volume's UUID.

Example:

```text
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /mnt/ebs-data ext4 defaults,nofail 0 2
```

Using the UUID is preferred over relying on a device name because device names can change.

---

## 11. Test `/etc/fstab`

```bash
sudo mount -a
```

Attempts to mount all filesystems listed in `/etc/fstab`.

If there is no output, the command generally completed successfully.

---

## 12. Verify the Final Mount

```bash
df -h
```

Confirm that `/mnt/ebs-data` appears in the output.

---

## 13. Verify the Filesystem

```bash
lsblk -f
```

Confirms that the EBS device has the expected filesystem and mount point.

---

## 14. Check `/etc/fstab`

```bash
cat /etc/fstab
```

Displays the current persistent mount configuration.

## Troubleshooting Commands

### Check mounted filesystems

```bash
mount
```

### Check disk usage

```bash
df -h
```

### Display block devices

```bash
lsblk
```

### Display filesystem information

```bash
lsblk -f
```

### Display device UUID

```bash
sudo blkid
```

### Check `/etc/fstab`

```bash
cat /etc/fstab
```

### Test `/etc/fstab`

```bash
sudo mount -a
```

## Important Notes

The exact Linux device name may differ depending on the EC2 instance type and Ubuntu version.

Always use `lsblk` to identify the correct device before formatting or mounting it.

Never run `mkfs` against a device until you have confirmed that it is the intended EBS volume.
