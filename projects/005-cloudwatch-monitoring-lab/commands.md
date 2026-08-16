# Ubuntu Tech Solutions — CloudWatch Monitoring Commands

This document contains the Linux commands used during the CloudWatch monitoring lab.

## 1. Check Running Processes and CPU Usage

```bash
top
```

### Purpose

Displays running processes and current system resource usage, including CPU utilization.

Press `q` to exit `top`.

---

## 2. Update Ubuntu Package Information

```bash
sudo apt update
```

### Purpose

Refreshes the local Ubuntu package index.

This was used before installing the CPU stress-testing utility.

---

## 3. Install Stress Testing Utility

```bash
sudo apt install stress -y
```

### Purpose

Installs the `stress` utility, which can generate artificial CPU workload for testing monitoring and alerting systems.

---

## 4. Generate CPU Load

```bash
stress --cpu 2 --timeout 300
```

### Purpose

Creates two CPU workers for approximately five minutes.

This is used to intentionally increase CPU utilization so that the CloudWatch alarm can be tested.

### Parameters

* `--cpu 2` — creates two CPU workers.
* `--timeout 300` — runs the workload for 300 seconds.

---

## 5. Stop the Stress Test

If the stress command is still running:

```text
Ctrl + C
```

### Purpose

Stops the running stress process and allows CPU utilization to return toward normal levels.

---

## 6. Verify CPU Usage After Testing

```bash
top
```

### Purpose

Used to verify that CPU utilization has decreased after stopping the artificial workload.

## Command Summary

| Command                        | Purpose                                 |
| ------------------------------ | --------------------------------------- |
| `top`                          | Monitor running processes and CPU usage |
| `sudo apt update`              | Update Ubuntu package information       |
| `sudo apt install stress -y`   | Install CPU stress-testing utility      |
| `stress --cpu 2 --timeout 300` | Generate CPU workload                   |
| `Ctrl + C`                     | Stop the stress test                    |
