# Linux System Logs, Performance Monitoring & Troubleshooting

## Table of Contents

1. [Introduction](#1-introduction)
2. [How to Check System Logs](#2-how-to-check-system-logs)
3. [Where Linux Logs Are Stored](#3-where-linux-logs-are-stored)
4. [VM Configuration Across Cloud Providers](#4-vm-configuration-across-cloud-providers)
5. [Create a VM Using the GCP CLI](#5-create-a-vm-using-the-gcp-cli)
6. [Ways to Connect to a VM](#6-ways-to-connect-to-a-vm)
7. [Check Linux System Performance](#7-check-linux-system-performance)
8. [Troubleshoot Disk Space Issues](#8-troubleshoot-disk-space-issues)
9. [Troubleshoot File Permission Issues](#9-troubleshoot-file-permission-issues)
10. [Troubleshoot Services](#10-troubleshoot-services)
11. [Find and Stop Processes](#11-find-and-stop-processes)
12. [Debug Errors and Hardware-Related Issues](#12-debug-errors-and-hardware-related-issues)
13. [A Practical Troubleshooting Workflow](#13-a-practical-troubleshooting-workflow)
14. [Quick Command Reference](#14-quick-command-reference)
15. [Session 09 — Linux Real-World Scenarios](#session-09--linux-real-world-scenarios)

---

## 1. Introduction

Linux troubleshooting is the process of identifying, investigating, and resolving problems affecting a Linux server or virtual machine (VM).

DevOps and cloud engineers troubleshoot issues such as:

- High CPU or memory utilization
- Low disk space or disk I/O problems
- Application or system services failing
- Incorrect file permissions
- Unexpected process behavior
- SSH or network connectivity failures
- Errors reported in system and application logs

The goal is to find the root cause, apply a safe fix, and verify that the system is working correctly.

## Session 09 — Linux Real-World Scenarios

**Program:** 55-Session Multi Cloud + DevOps with AI Program  
**Module:** Linux Administration for DevOps + GCP  
**Live session:** Vikas Ratnawat

### Key learnings

- **Assess → Investigate → Fix → Verify → Document:** Follow a structured framework when responding to production incidents. First assess the impact, investigate the evidence, apply a safe fix, verify recovery, and document the cause and resolution.
- **Monitor disk space:** A full disk is a common cause of service and application failures. Check disk usage regularly with `df -h`. A scheduled daily check can help identify growing usage early, but production environments should use appropriate monitoring and alerts as well.
- **Check inode usage:** Run `df -i` to check inode utilization. A filesystem can run out of inodes even when `df -h` shows free disk space, especially when many small files exist.

Example commands:

```bash
# Check filesystem disk space
df -h

# Check inode usage
df -i
```

Optional simple daily check using cron:

```bash
# Edit the current user's crontab
crontab -e
```

For example, add this line to run a basic disk-space report every day at 9:00 AM:

```cron
0 9 * * * /bin/df -h
```

This example writes command output through cron's configured mail/output handling; it does not automatically create a monitoring alert. For production use, configure centralized monitoring, alerting, and log retention according to your environment.

---

## 2. How to Check System Logs

System logs help us understand what happened on a Linux server, including errors, warnings, service failures, authentication events, and system startup messages.

### Method 1: Check logs manually on the Linux server

Connect to the VM, navigate to the relevant log location, and use Linux commands to inspect the logs.

```bash
cd /var/log
ls -lah
```

Useful commands:

```bash
# Display the latest lines of a log file
sudo tail -n 50 /var/log/syslog

# Follow new log entries in real time
sudo tail -f /var/log/syslog

# Search for errors in a log file
sudo grep -i "error" /var/log/syslog

# Search for errors or failures
sudo grep -Ei "error|failed|failure" /var/log/syslog
```

**Note:** Log filenames vary by Linux distribution. Ubuntu commonly uses `/var/log/syslog`; some distributions use `/var/log/messages`. Application logs may be stored in separate directories.

### Method 2: Use centralized logging and observability tools

Tools can collect, search, filter, and visualize logs from multiple servers in one place.

Examples:

- **Kibana** — searches and visualizes logs indexed in Elasticsearch.
- **Splunk** — collects and searches machine-generated data, including logs.
- **Dynatrace** — provides observability features, including log analysis.
- **Grafana Loki** — aggregates and queries logs.
- **Cloud-native logging** — services such as Google Cloud Logging, Amazon CloudWatch Logs, and Azure Monitor Logs.

Centralized logging is useful when an organization manages many VMs, containers, or application services.

### View logs with systemd

Many modern Linux distributions use `systemd` and `journald`.

```bash
# View recent system logs
sudo journalctl -n 100

# Follow logs in real time
sudo journalctl -f

# View logs for a specific service
sudo journalctl -u nginx

# View logs from the current boot
sudo journalctl -b

# Show errors and higher-priority messages
sudo journalctl -p err
```

Use `sudo` when elevated permissions are required.

## 3. Where Linux Logs Are Stored

The `/var/log` directory is a common location for Linux system and application log files.

| Location or command | Purpose |
|---|---|
| `/var/log/syslog` | General system messages on many Debian/Ubuntu systems |
| `/var/log/messages` | General system messages on some distributions |
| `/var/log/auth.log` | Authentication and authorization events on Ubuntu/Debian |
| `/var/log/secure` | Authentication-related events on some RHEL-based systems |
| `/var/log/kern.log` | Kernel messages on some distributions |
| `/var/log/nginx/` | Nginx access and error logs, if configured there |
| `journalctl` | Reads systemd journal entries |

Not every file exists on every Linux distribution. Log rotation, permissions, and application configuration can also affect what is available.

## 4. VM Configuration Across Cloud Providers

A VM configuration describes resources such as virtual CPUs, memory, and storage.

Example target configuration:

- **CPU:** 16 vCPUs
- **RAM:** 16 GiB
- **Storage:** 512 GB SSD

Illustrative cloud machine types discussed in training:

| Cloud provider | Machine type mentioned | Important note |
|---|---|---|
| AWS | `c6.4xlarge` | Typically 16 vCPUs and 32 GiB RAM, so it does **not** match a 16 GiB RAM target |
| Google Cloud | `n2-standard-16` | 16 vCPUs and 64 GiB memory, so it does **not** match a 16 GiB RAM target |
| Azure | `Standard_F16s_v2` | 16 vCPUs and typically 32 GiB RAM, so it does **not** match a 16 GiB RAM target |

**Important:** These named instance types are not equivalent configurations. If the requirement is exactly 16 vCPUs and 16 GiB RAM, check each provider's current VM catalog for a suitable type. Storage is usually configured separately and may use different disk types and limits. Verify current specifications and regional availability before provisioning.

## 5. Create a VM Using the GCP CLI

The Google Cloud CLI (`gcloud`) can create and manage Compute Engine VMs from a terminal.

### Example command

Replace the placeholder project ID and VM name with your actual values.

```bash
gcloud compute instances create VM_NAME \
  --project=PROJECT_ID \
  --zone=us-central1-c \
  --machine-type=e2-medium
```

Example with a boot disk size:

```bash
gcloud compute instances create VM_NAME \
  --project=PROJECT_ID \
  --zone=us-central1-c \
  --machine-type=e2-medium \
  --boot-disk-size=20GB
```

### Before running the command

1. Install the Google Cloud CLI, if needed.
2. Authenticate using `gcloud auth login` or your organization's approved authentication method.
3. Set or specify the correct project.
4. Confirm the selected zone and machine type.
5. Confirm permissions, network settings, and cost implications.

Useful commands:

```bash
# Check the active account
gcloud auth list

# List configured projects accessible to you
gcloud projects list

# List VM instances in a zone
gcloud compute instances list --zones=us-central1-c

# Describe a specific VM
gcloud compute instances describe VM_NAME \
  --zone=us-central1-c \
  --project=PROJECT_ID
```

`e2-medium` is an example machine type, not a 16-vCPU machine. Select a machine type based on the workload requirements.

## 6. Ways to Connect to a VM

Common connection methods include:

1. **Browser-based SSH:** Connect through the cloud provider's console when supported and authorized.
2. **Cloud CLI:** Use commands such as `gcloud compute ssh` for Google Cloud VMs.
3. **SSH key authentication:** Use a public/private key pair. The public key is configured for the server or cloud account; the private key must be kept secure.
4. **Custom SSH port:** Connect with `ssh -p PORT USER@HOST` if SSH is configured to listen on a non-default port and network rules allow it.
5. **Third-party SSH clients:** Tools such as PuTTY or other SSH clients can connect when correctly configured.

Example:

```bash
gcloud compute ssh VM_NAME \
  --zone=us-central1-c \
  --project=PROJECT_ID
```

Standard SSH example:

```bash
ssh USER@SERVER_IP
```

Custom port example:

```bash
ssh -p 2222 USER@SERVER_IP
```

**Security reminder:** Do not share private keys. Restrict firewall rules, use approved identity and access controls, and avoid exposing SSH to the public internet unnecessarily.

## 7. Check Linux System Performance

Performance commands help identify resource bottlenecks.

### CPU and process usage

```bash
top
```

`top` displays running processes and resource utilization, including CPU and memory.

If installed, `htop` provides an interactive, more visual process monitor:

```bash
htop
```

### Memory usage

```bash
free -h
```

Displays memory and swap usage in a human-readable format. On Linux, available memory is often more useful than the raw free-memory value.

### Disk space

```bash
df -h
```

Shows filesystem capacity, used space, and available space.

### Disk usage by directory

```bash
du -sh /var/log
du -sh /home/*
```

`du` estimates disk space used by files and directories. It does **not** show current users.

### Process list

```bash
ps aux
```

Shows running processes. To search for a process:

```bash
ps aux | grep nginx
```

### System uptime and load

```bash
uptime
```

Shows how long the system has been running, logged-in users, and load averages.

### Disk I/O statistics

```bash
iostat -xz 1
```

Shows CPU and disk I/O statistics if the `sysstat` package is installed.

### Current logged-in users

```bash
who
w
```

Use `who` or `w` to see logged-in users and session information.

## 8. Troubleshoot Disk Space Issues

### Why disk troubleshooting matters

A full disk can cause application failures, failed deployments, database issues, and inability to write logs or temporary files.

### Step 1: Check filesystem usage

```bash
df -h
df -i
```

- `df -h` checks space usage.
- `df -i` checks inode usage, which can be exhausted even when disk space remains.

### Step 2: Find large directories and files

```bash
sudo du -xhd1 /var 2>/dev/null
sudo find /var -type f -size +500M -print
```

Review the output before deleting anything.

### Step 3: Check logs and cleanup safely

- Inspect log rotation and application logging configuration.
- Remove only files confirmed to be unnecessary and safe to delete.
- Use the package manager's cleanup tools when appropriate.
- Expand the disk or filesystem if the workload genuinely needs more capacity.

**Do not blindly delete log files or application data.** Identify the cause first and follow your organization's retention and backup policies.

## 9. Troubleshoot File Permission Issues

### Why permissions matter

Incorrect ownership or permissions can prevent an application or user from reading, writing, or executing required files. Overly broad permissions can also create security risks.

### Inspect permissions and ownership

```bash
ls -l filename
stat filename
namei -l /path/to/file
```

### Change file permissions

```bash
chmod 640 filename
```

This gives the owner read/write access, the group read access, and others no access.

### Change ownership

```bash
sudo chown USER:GROUP filename
```

Replace `USER` and `GROUP` with the intended account and group.

**Best practice:** Apply the minimum permissions required. Avoid using `chmod 777` as a routine fix.

## 10. Troubleshoot Services

A service may fail because of configuration errors, missing files, permission problems, unavailable ports, or dependency failures.

### Check service status

```bash
sudo systemctl status nginx
```

### Start, stop, or restart a service

```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
```

Restart only when appropriate, since it may interrupt service.

### Enable a service at boot

```bash
sudo systemctl enable nginx
```

### Inspect service logs

```bash
sudo journalctl -u nginx -n 100
sudo journalctl -u nginx -f
```

### Check whether a port is listening

```bash
sudo ss -lntp
```

Check the service configuration before restarting it. For Nginx, for example:

```bash
sudo nginx -t
```

## 11. Find and Stop Processes

### Find a process

```bash
ps aux
pgrep -a nginx
```

### Stop a process gracefully

```bash
kill PID
```

Replace `PID` with the process ID.

### Force-stop a process only if necessary

```bash
kill -9 PID
```

`kill -9` terminates a process immediately and does not allow it to clean up. Prefer a normal termination first.

### Identify which process uses a port

```bash
sudo ss -lntp
```

On systems with `lsof` installed:

```bash
sudo lsof -i :8080
```

Before stopping a process, verify its identity and impact. Avoid terminating critical system processes without understanding the consequences.

## 12. Debug Errors and Hardware-Related Issues

### Debug errors

1. Identify the symptom and when it started.
2. Check system and application logs.
3. Check service status and configuration.
4. Review recent deployments or configuration changes.
5. Check CPU, memory, disk, and network usage.
6. Reproduce the issue safely when possible.
7. Apply a targeted fix and verify the result.

Useful commands:

```bash
sudo journalctl -p err -b
dmesg -T | tail -n 100
systemctl --failed
```

### Troubleshoot hardware-related or VM infrastructure issues

For a cloud VM, many underlying hardware components are managed by the cloud provider. An engineer can still investigate guest-visible symptoms such as:

- Disk I/O errors or filesystem problems
- Memory pressure or out-of-memory events
- Unexpected VM restarts
- Network interface or connectivity issues
- Kernel or device errors

Useful commands:

```bash
dmesg -T
free -h
df -h
uptime
ip addr
ip route
```

For suspected host-level hardware failures, check cloud provider health events, monitoring metrics, and support guidance. Do not assume every VM issue is caused by physical hardware.

## 13. A Practical Troubleshooting Workflow

Use this checklist when a Linux VM or application is experiencing problems.

1. **Understand the issue:** What is failing, and when did it begin?
2. **Check logs:** Review `/var/log` and/or `journalctl`.
3. **Check system health:** Run `top`, `free -h`, `df -h`, and `uptime`.
4. **Check processes:** Use `ps`, `pgrep`, or `htop`.
5. **Check services:** Use `systemctl status SERVICE_NAME`.
6. **Check networking:** Review `ip addr`, `ip route`, and listening ports with `ss`.
7. **Identify the root cause:** Avoid making changes based only on assumptions.
8. **Apply a safe fix:** Follow access, backup, and change-management practices.
9. **Verify recovery:** Recheck logs, service health, and application behavior.
10. **Document the incident:** Record the cause, fix, and prevention steps.

## 14. Quick Command Reference

| Command | Purpose |
|---|---|
| `tail -f /var/log/syslog` | Follow log entries in real time (if this file exists) |
| `journalctl -f` | Follow systemd journal entries |
| `journalctl -u nginx` | View logs for a service |
| `top` | Monitor processes and CPU/memory usage |
| `htop` | Interactive process monitor, if installed |
| `free -h` | Check memory and swap |
| `df -h` | Check filesystem space |
| `df -i` | Check inode usage |
| `du -sh DIRECTORY` | Estimate directory disk usage |
| `ps aux` | List running processes |
| `uptime` | Check uptime and load averages |
| `who` / `w` | Check logged-in users |
| `systemctl status SERVICE` | Check service status |
| `systemctl --failed` | List failed systemd units |
| `ss -lntp` | Show listening TCP ports and associated processes, when permitted |
| `dmesg -T` | Review kernel messages |
| `chmod` | Change file permissions |
| `chown` | Change file ownership |

---

## Key Takeaways

- Logs help identify errors and understand system behavior.
- Centralized logging tools make it easier to investigate issues across multiple servers.
- Performance checks should cover CPU, memory, disk space, disk I/O, processes, and load.
- Fix permissions and disk issues only after identifying the cause.
- Cloud VM machine types differ in CPU, memory, and storage; always verify specifications before provisioning.
- Troubleshooting should be systematic: observe, investigate, fix, verify, and document.
