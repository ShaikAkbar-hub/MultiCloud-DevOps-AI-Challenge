# 🔥 Session 07 COMPLETE — 55-Session Multi Cloud + DevOps with AI Program

📚 Topic: Linux System Administration
📦 Module: Linux Administration for DevOps + GCP


💡 What I learned in today's LIVE session with Vikas Ratnawat:

✅ df -h is the first command to run on any server — full disk means server crash
✅ systemctl enable nginx starts on every boot systemctl start nginx starts right now
✅ top shows real-time CPU and memory — press P to sort by CPU usage


# 🐧 Linux DevOps Commands — Simple Explanations, Real-Time Scenarios & Practical Examples

These notes explain Linux commands in simple English for beginners learning Linux and DevOps.

For every command, understand:

What it means: What the command does.

When to use it: The situation in which it is useful.

Real-time scenario: A practical problem you might face as a DevOps engineer.


## Table of Contents

- [💽 Disk Monitoring Commands](#disk-monitoring-commands)
  - [df -h — Check Disk Space](#df-h-check-disk-space)
  - [df -Th — Check Disk Space and Filesystem Type](#df-th-check-disk-space-and-filesystem-type)
  - [du -sh /var/log — Check the Size of the Log Directory](#du-sh-varlog-check-the-size-of-the-log-directory)
  - [du -h --max-depth=1 /var — Find Large Directories](#du-h-max-depth1-var-find-large-directories)
  - [lsblk — List Disks and Partitions](#lsblk-list-disks-and-partitions)
  - [lsblk -f — Check Filesystems and Mountpoints](#lsblk-f-check-filesystems-and-mountpoints)
  - [find /var/log -type f -size +100M — Find Large Log Files](#find-varlog-type-f-size-100m-find-large-log-files)
  - [df -i — Check Inode Usage](#df-i-check-inode-usage)
- [🧠 CPU Monitoring Commands](#cpu-monitoring-commands)
  - [top — Monitor CPU, RAM and Running Processes](#top-monitor-cpu-ram-and-running-processes)
  - [uptime — Check Uptime and System Load](#uptime-check-uptime-and-system-load)
  - [lscpu — Check CPU Information](#lscpu-check-cpu-information)
  - [nproc — Count Available Processing Units](#nproc-count-available-processing-units)
  - [ps aux — List Running Processes](#ps-aux-list-running-processes)
  - [ps aux --sort=-%cpu | head — Find CPU-Heavy Processes](#ps-aux-sort-cpu-head-find-cpu-heavy-processes)
  - [ps -ef — Display Processes in Full Format](#ps-ef-display-processes-in-full-format)
  - [vmstat 1 5 — Check System Activity](#vmstat-1-5-check-system-activity)
- [🧮 RAM and Memory Monitoring Commands](#ram-and-memory-monitoring-commands)
  - [free -h — Check Available RAM](#free-h-check-available-ram)
  - [free -m — Display Memory in Megabytes](#free-m-display-memory-in-megabytes)
  - [cat /proc/meminfo — Inspect Detailed Memory Information](#cat-procmeminfo-inspect-detailed-memory-information)
  - [ps aux --sort=-%mem | head — Find Memory-Heavy Processes](#ps-aux-sort-mem-head-find-memory-heavy-processes)
  - [watch -n 2 free -h — Monitor Memory Continuously](#watch-n-2-free-h-monitor-memory-continuously)
  - [swapon --show — Check Swap Usage](#swapon-show-check-swap-usage)
  - [vmstat -s — Display System Statistics](#vmstat-s-display-system-statistics)
- [⚙️ Service Management Commands](#service-management-commands)
  - [systemctl status nginx — Check Service Status](#systemctl-status-nginx-check-service-status)
  - [systemctl start nginx — Start a Service Now](#systemctl-start-nginx-start-a-service-now)
  - [systemctl enable nginx — Start a Service Automatically at Boot](#systemctl-enable-nginx-start-a-service-automatically-at-boot)
  - [systemctl enable --now nginx — Enable and Start a Service](#systemctl-enable-now-nginx-enable-and-start-a-service)
  - [systemctl stop nginx — Stop a Service](#systemctl-stop-nginx-stop-a-service)
  - [systemctl restart nginx — Restart a Service](#systemctl-restart-nginx-restart-a-service)
  - [systemctl reload nginx — Reload Configuration](#systemctl-reload-nginx-reload-configuration)
  - [systemctl is-active nginx — Check Whether a Service Is Active](#systemctl-is-active-nginx-check-whether-a-service-is-active)
  - [systemctl is-enabled nginx — Check Startup Configuration](#systemctl-is-enabled-nginx-check-startup-configuration)
  - [systemctl --failed — Find Failed Services](#systemctl-failed-find-failed-services)
  - [systemctl list-units --type=service — List Loaded Services](#systemctl-list-units-typeservice-list-loaded-services)
- [📜 Log and Troubleshooting Commands](#log-and-troubleshooting-commands)
  - [journalctl -u nginx -n 50 — View Recent Service Logs](#journalctl-u-nginx-n-50-view-recent-service-logs)
  - [journalctl -u nginx -f — Follow Service Logs Live](#journalctl-u-nginx-f-follow-service-logs-live)
  - [journalctl -b — View Logs from the Current Boot](#journalctl-b-view-logs-from-the-current-boot)
  - [journalctl -p err -b — Find Error Messages](#journalctl-p-err-b-find-error-messages)
  - [journalctl -u jenkins --since today — View Today's Jenkins Logs](#journalctl-u-jenkins-since-today-view-todays-jenkins-logs)
  - [dmesg -T — Check Kernel Messages](#dmesg-t-check-kernel-messages)
  - [tail -n 50 /var/log/syslog — Read Recent System Logs](#tail-n-50-varlogsyslog-read-recent-system-logs)
  - [tail -f /var/log/syslog — Watch a Log File Live](#tail-f-varlogsyslog-watch-a-log-file-live)
  - [grep -i error /var/log/syslog — Search for Errors](#grep-i-error-varlogsyslog-search-for-errors)
  - [last — Check Recorded Login Sessions](#last-check-recorded-login-sessions)
  - [who — Check Currently Logged-In Users](#who-check-currently-logged-in-users)
- [🌐 Networking Commands](#networking-commands)
  - [ip addr — Check Network Interfaces and IP Addresses](#ip-addr-check-network-interfaces-and-ip-addresses)
  - [ip route — Check Network Routes](#ip-route-check-network-routes)
  - [ping -c 4 google.com — Test Basic Network Reachability](#ping-c-4-googlecom-test-basic-network-reachability)
  - [ss -tulpn — Check Listening Network Ports](#ss-tulpn-check-listening-network-ports)
  - [curl -I https://example.com — Check an HTTP Endpoint](#curl-i-httpsexamplecom-check-an-http-endpoint)
  - [hostname — Check the Server Name](#hostname-check-the-server-name)
  - [getent hosts example.com — Check Hostname Resolution](#getent-hosts-examplecom-check-hostname-resolution)
- [🔎 Process Management Commands](#process-management-commands)
  - [ps -p 1234 -o pid,ppid,cmd,%cpu,%mem — Inspect a Specific Process](#ps-p-1234-o-pidppidcmdcpumem-inspect-a-specific-process)
  - [pgrep nginx — Find a Process by Name](#pgrep-nginx-find-a-process-by-name)
  - [pstree -p — View Process Relationships](#pstree-p-view-process-relationships)
  - [kill -15 1234 — Request a Graceful Process Shutdown](#kill-15-1234-request-a-graceful-process-shutdown)
  - [kill -9 1234 — Force a Process to Stop](#kill-9-1234-force-a-process-to-stop)
- [🛠️ General Linux Commands](#general-linux-commands)
  - [uname -a — Check Kernel and System Information](#uname-a-check-kernel-and-system-information)
  - [date — Check System Date and Time](#date-check-system-date-and-time)
  - [whoami — Check the Current User](#whoami-check-the-current-user)
  - [id — Check User and Group Information](#id-check-user-and-group-information)
  - [pwd — Check the Current Directory](#pwd-check-the-current-directory)
  - [ls -lah — List Files and Directories](#ls-lah-list-files-and-directories)
  - [free -h && df -h — Check RAM and Disk Together](#free-h-df-h-check-ram-and-disk-together)
  - [watch -n 2 'df -h; free -h' — Monitor Disk and RAM Continuously](#watch-n-2-df-h-free-h-monitor-disk-and-ram-continuously)

## 💽 Disk Monitoring Commands

### df -h — Check Disk Space

**Command:**

```bash
df -h
```

**Simple explanation:**

This command checks how much storage space your Linux server has, how much is already occupied, and how much space is left.

Think of your server's disk as a cupboard. df -h tells you how much of the cupboard is full and how much space remains.

**When do we use it:**

When an application cannot save files, uploads fail, or a server reports that there is not enough storage space.

**Real-time scenario:**

Your manager reports that an application is failing to upload files. You run df -h and discover that the filesystem is 98% full. You investigate what is consuming the space before deciding how to resolve the problem.

### df -Th — Check Disk Space and Filesystem Type

**Command:**

```bash
df -Th
```

**Simple explanation:**

This command shows disk usage and the type of filesystem used for each mounted storage area.

A filesystem is the method Linux uses to organize files on storage, such as ext4 or XFS.

**When do we use it:**

When investigating storage problems or checking the type of filesystem used by a mounted disk.

**Real-time scenario:**

You attach a new storage volume to a Linux server. You use df -Th to check the mounted filesystems, their available space and their filesystem types.

### du -sh /var/log — Check the Size of the Log Directory

**Command:**

```bash
du -sh /var/log
```

**Simple explanation:**

This command calculates how much disk space the /var/log directory occupies.

The /var/log directory commonly contains logs that record system and application activity.

du checks the space used by files and directories.

-s displays a summary.

-h displays the size in readable units such as MB or GB.

**When do we use it:**

When the disk is almost full and you suspect that log files are consuming too much storage.

**Real-time scenario:**

Your server's disk usage is 95%. You run du -sh /var/log and discover that the log directory occupies 4 GB. You investigate the large logs and configure appropriate log rotation or retention instead of deleting files blindly.

### du -h --max-depth=1 /var — Find Large Directories

**Command:**

```bash
du -h --max-depth=1 /var
```

**Simple explanation:**

This command shows the sizes of directories directly inside /var, helping you understand which directories occupy the most storage.

**When do we use it:**

When df -h shows that a disk is almost full, but you do not yet know which directory is responsible.

**Real-time scenario:**

Your application server has very little disk space remaining. You use this command to compare the sizes of directories under /var, such as /var/log and /var/cache, and investigate the largest ones.

### lsblk — List Disks and Partitions

**Command:**

```bash
lsblk
```

**Simple explanation:**

This command displays the storage disks and partitions that Linux can see.

A disk is the storage device. A partition is a section of that disk that can be used for a filesystem or another storage purpose.

**When do we use it:**

When a new disk is attached to a Linux virtual machine and you want to check whether the operating system detects it.

**Real-time scenario:**

You attach a new 50 GB EBS volume to an AWS EC2 instance. You run lsblk to check whether the new disk appears.

### lsblk -f — Check Filesystems and Mountpoints

**Command:**

```bash
lsblk -f
```

**Simple explanation:**

This command displays disks and partitions along with filesystem information, UUIDs and mountpoints.

A mountpoint is the directory through which you access a mounted filesystem.

**When do we use it:**

When investigating why a disk is not accessible or checking whether a disk has a filesystem and is mounted.

**Real-time scenario:**

A new disk appears in lsblk, but your application cannot access it. You run lsblk -f to investigate whether a filesystem exists and whether the disk is mounted.

### find /var/log -type f -size +100M — Find Large Log Files

**Command:**

```bash
find /var/log -type f -size +100M
```

**Simple explanation:**

This command searches for regular files larger than 100 MB inside /var/log.

find searches for files and directories.

-type f selects regular files.

-size +100M selects files larger than 100 MB.

**When do we use it:**

When a log directory is consuming too much storage and you want to identify unusually large files.

**Real-time scenario:**

Your application server's disk is nearly full. You use this command to find large logs that may be growing because of repeated application errors.

The command finds files but does not delete them.

### df -i — Check Inode Usage

**Command:**

```bash
df -i
```

**Simple explanation:**

Linux uses inodes to keep track of files and directories. A filesystem can run out of available inodes even when it still has free disk space.

This command shows inode usage.

**When do we use it:**

When an application cannot create new files even though df -h shows that disk space remains.

**Real-time scenario:**

A server has thousands of tiny temporary files. The disk has free space, but the application cannot create more files. You run df -i to check whether the filesystem has run out of inodes.

## 🧠 CPU Monitoring Commands

### top — Monitor CPU, RAM and Running Processes

**Command:**

```bash
top
```

**Simple explanation:**

This command displays running programs and their CPU and memory usage. The information updates continuously.

A process is a running instance of a program.

**When do we use it:**

When a server becomes slow or you suspect that a program is consuming too many resources.

**Real-time scenario:**

Your website is responding slowly. You run top and discover that a Java process is using a very high percentage of CPU. You investigate that application's workload and logs.

Useful keys:

Press P to sort by CPU usage.

Press M to sort by memory usage.

Press q to exit.

### uptime — Check Uptime and System Load

**Command:**

```bash
uptime
```

**Simple explanation:**

This command shows how long the server has been running and its average system load over the last 1, 5 and 15 minutes.

System load indicates how many tasks are running or waiting for resources, including certain disk operations.

**When do we use it:**

When checking whether a server recently restarted or investigating whether its workload has increased.

**Real-time scenario:**

Your manager reports that the server has become slow. You run uptime and notice that the load average has increased significantly compared with the available CPU capacity. You investigate further using top and vmstat.

A high load average does not automatically mean CPU usage is high.

### lscpu — Check CPU Information

**Command:**

```bash
lscpu
```

**Simple explanation:**

This command displays information about the server's processor, including its architecture, CPU count and core details.

**When do we use it:**

When checking the CPU capacity or architecture of a Linux server.

**Real-time scenario:**

You are preparing a Linux server for an application deployment. You run lscpu to understand the available processor configuration before investigating whether the server has sufficient capacity.

### nproc — Count Available Processing Units

**Command:**

```bash
nproc
```

**Simple explanation:**

This command displays the number of processing units available to the current process.

**When do we use it:**

When checking how many processing units are available to a workload or script.

**Real-time scenario:**

You are running a parallel build and want to check the available processing capacity. You run nproc before deciding how much parallel work to configure.

### ps aux — List Running Processes

**Command:**

```bash
ps aux
```

**Simple explanation:**

This command displays a snapshot of running processes, including their owners and resource usage.

Unlike top, the output does not continuously refresh.

**When do we use it:**

When you want a list of running programs to investigate a particular process.

**Real-time scenario:**

An application is behaving unexpectedly. You run ps aux to check whether its process is running and identify its process ID.

### ps aux --sort=-%cpu | head — Find CPU-Heavy Processes

**Command:**

```bash
ps aux --sort=-%cpu | head
```

**Simple explanation:**

This command sorts processes by CPU usage, highest first, and displays the first 10 lines by default.

**When do we use it:**

When you want to identify which processes are consuming the most CPU without opening an interactive monitor.

**Real-time scenario:**

A server is experiencing high CPU usage. You run this command to identify the busiest processes and investigate the one consuming the most resources.

### ps -ef — Display Processes in Full Format

**Command:**

```bash
ps -ef
```

**Simple explanation:**

This command displays running processes with details such as process ID, parent process ID, user and command.

A parent process is the process that started another process.

**When do we use it:**

When investigating how a program was started or checking the relationship between processes.

**Real-time scenario:**

A script starts a background process that you cannot easily identify. You use ps -ef to inspect the running processes and their parent process IDs.

### vmstat 1 5 — Check System Activity

**Command:**

```bash
vmstat 1 5
```

**Simple explanation:**

This command reports information about memory, CPU activity, processes and input/output activity at one-second intervals for five reports.

**When do we use it:**

When investigating whether slowness might be caused by CPU pressure, memory pressure or storage activity.

**Real-time scenario:**

Your application is slow, but you do not know why. You run vmstat 1 5 to gather more information about system activity before deciding what to investigate next.

The first report often reflects averages since boot. Subsequent reports are generally more useful for current activity.

## 🧮 RAM and Memory Monitoring Commands

### free -h — Check Available RAM

**Command:**

```bash
free -h
```

**Simple explanation:**

This command shows how much RAM your server has, how much is being used and how much is estimated to be available.

RAM is temporary memory used by running programs.

**When do we use it:**

When an application becomes slow, a build fails unexpectedly, or you suspect that the server is running out of memory.

**Real-time scenario:**

A Jenkins build fails on a Linux server. You run free -h and discover that available memory is very low. You investigate the processes using the most RAM and check whether the system is swapping heavily.

Remember: high memory usage alone does not prove that RAM is exhausted. Pay attention to available memory.

### free -m — Display Memory in Megabytes

**Command:**

```bash
free -m
```

**Simple explanation:**

This command displays memory figures in megabytes instead of automatically selecting readable units.

**When do we use it:**

When you want memory figures displayed in MB for easier comparison or reporting.

**Real-time scenario:**

You are comparing memory consumption across several smaller Linux servers. You use free -m to view their memory figures in the same unit.

### cat /proc/meminfo — Inspect Detailed Memory Information

**Command:**

```bash
cat /proc/meminfo
```

**Simple explanation:**

This command displays detailed information about how Linux is using and managing memory.

The /proc directory contains information that the Linux kernel exposes about the running system.

**When do we use it:**

When free -h does not provide enough detail to investigate a memory problem.

**Real-time scenario:**

Your server is experiencing memory pressure. You inspect /proc/meminfo to investigate details such as available memory, cached memory and swap-related information.

### ps aux --sort=-%mem | head — Find Memory-Heavy Processes

**Command:**

```bash
ps aux --sort=-%mem | head
```

**Simple explanation:**

This command sorts running processes by memory usage and displays the highest-consuming ones first.

**When do we use it:**

When the server's RAM usage is high and you need to identify which applications are consuming the most memory.

**Real-time scenario:**

A Linux server has only a small amount of available memory. You run this command and discover that a Java application is using a large amount of RAM. You investigate its workload and memory configuration before taking action.

### watch -n 2 free -h — Monitor Memory Continuously

**Command:**

```bash
watch -n 2 free -h
```

**Simple explanation:**

This command repeats free -h every two seconds so you can observe changes in memory usage.

**When do we use it:**

When checking whether memory usage increases as an application starts or a build runs.

**Real-time scenario:**

You suspect that a build process is consuming increasing amounts of RAM. You use this command to observe memory usage while the build runs.

Press Ctrl+C to stop monitoring.

### swapon --show — Check Swap Usage

**Command:**

```bash
swapon --show
```

**Simple explanation:**

Swap is disk space that Linux can use as an extension of memory. It is slower than RAM.

This command shows the swap areas currently enabled on the system.

**When do we use it:**

When investigating memory pressure or checking whether swap has been configured.

**Real-time scenario:**

Your server becomes slow when several applications run simultaneously. You check swap usage to investigate whether the system may be moving memory pages to disk because RAM is under pressure.

### vmstat -s — Display System Statistics

**Command:**

```bash
vmstat -s
```

**Simple explanation:**

This command displays summary statistics about memory and other system activity.

**When do we use it:**

When you need additional system statistics during troubleshooting.

**Real-time scenario:**

You are investigating a server that has been experiencing memory pressure. You use vmstat -s to gather more memory-related information alongside free -h.

## ⚙️ Service Management Commands

A service is a program that runs in the background, such as Nginx, Jenkins or Docker.

The following examples use systemd, which is the service manager on many modern Linux distributions.

### systemctl status nginx — Check Service Status

**Command:**

```bash
systemctl status nginx
```

**Simple explanation:**

This command checks whether the Nginx service is running, stopped or has failed.

**When do we use it:**

When a website is unavailable and you want to check whether the web server is running.

**Real-time scenario:**

Your manager reports that the website is not opening. You run systemctl status nginx to check the service status before attempting to restart anything.

Nginx must be installed, and the service may have a different name on your system.

### systemctl start nginx — Start a Service Now

**Command:**

```bash
sudo systemctl start nginx
```

**Simple explanation:**

This command starts the Nginx service immediately. It does not, by itself, configure Nginx to start automatically at boot.

**When do we use it:**

When a service is stopped and you need to start it.

**Real-time scenario:**

You check Nginx and discover that it is inactive. After confirming that it is appropriate to start the service, you run this command and verify the result.

### systemctl enable nginx — Start a Service Automatically at Boot

**Command:**

```bash
sudo systemctl enable nginx
```

**Simple explanation:**

This command configures Nginx to start automatically when the server boots.

**When do we use it:**

When you want a service to start automatically after a server restart.

**Real-time scenario:**

You configure Nginx on a new Linux server. You enable it so that it is configured to start automatically after future reboots.

Important: enable does not necessarily start the service immediately.

### systemctl enable --now nginx — Enable and Start a Service

**Command:**

```bash
sudo systemctl enable --now nginx
```

**Simple explanation:**

This command configures Nginx to start automatically at boot and also starts it now.

**When do we use it:**

When setting up a service that should be running immediately and should also start after future reboots.

**Real-time scenario:**

You finish configuring a new Nginx server and want the service to run now and start automatically when the virtual machine restarts.

### systemctl stop nginx — Stop a Service

**Command:**

```bash
sudo systemctl stop nginx
```

**Simple explanation:**

This command stops the Nginx service.

**When do we use it:**

When a service needs to be stopped for maintenance or when an authorized troubleshooting procedure requires it.

**Real-time scenario:**

You need to perform planned maintenance on a web server. After confirming the impact and maintenance window, you stop the service.

Do not stop production services without checking their impact.

### systemctl restart nginx — Restart a Service

**Command:**

```bash
sudo systemctl restart nginx
```

**Simple explanation:**

This command stops the service and starts it again.

**When do we use it:**

When a service needs a full restart after a configuration change or another corrective action.

**Real-time scenario:**

After correcting a problem that requires a restart, you restart Nginx and check its status to confirm that it is running.

A restart can temporarily interrupt service.

### systemctl reload nginx — Reload Configuration

**Command:**

```bash
sudo systemctl reload nginx
```

**Simple explanation:**

This asks Nginx to reload its configuration without performing a full service restart, when supported.

**When do we use it:**

When you change a supported configuration and want the service to apply the changes without a full restart.

**Real-time scenario:**

You update an Nginx configuration file. You validate the configuration, reload the service and test whether the new configuration works.

### systemctl is-active nginx — Check Whether a Service Is Active

**Command:**

```bash
systemctl is-active nginx
```

**Simple explanation:**

This command checks the current active state of the Nginx service.

**When do we use it:**

When you need a quick status check, especially inside a troubleshooting script.

**Real-time scenario:**

A monitoring script needs to check whether Nginx is active. You use systemctl is-active nginx to get the service state.

### systemctl is-enabled nginx — Check Startup Configuration

**Command:**

```bash
systemctl is-enabled nginx
```

**Simple explanation:**

This command checks whether Nginx is configured to start automatically at boot.

**When do we use it:**

When verifying the service's startup configuration.

**Real-time scenario:**

After setting up a server, you want to confirm that Nginx is configured to start after the machine reboots.

### systemctl --failed — Find Failed Services

**Command:**

```bash
systemctl --failed
```

**Simple explanation:**

This command displays systemd units that have failed.

**When do we use it:**

When investigating services that failed to start or other systemd-managed components that have failed.

**Real-time scenario:**

After a server restart, an application is unavailable. You run systemctl --failed to check whether any services failed during startup.

### systemctl list-units --type=service — List Loaded Services

**Command:**

```bash
systemctl list-units --type=service
```

**Simple explanation:**

This command lists service units currently loaded by systemd, including their states.

**When do we use it:**

When you need to inspect the services currently known to systemd.

**Real-time scenario:**

You are troubleshooting a server and need to identify the relevant service name before checking its status.

## 📜 Log and Troubleshooting Commands

Logs are records of events that happen on a server or application. They help you understand what happened before or during a failure.

### journalctl -u nginx -n 50 — View Recent Service Logs

**Command:**

```bash
sudo journalctl -u nginx -n 50
```

**Simple explanation:**

This command displays the latest 50 journal entries associated with Nginx.

**When do we use it:**

When Nginx fails to start, behaves unexpectedly or needs troubleshooting.

**Real-time scenario:**

Your website is unavailable. You check Nginx's status and discover that the service has failed. You inspect its recent logs to find clues about the cause.

### journalctl -u nginx -f — Follow Service Logs Live

**Command:**

```bash
sudo journalctl -u nginx -f
```

**Simple explanation:**

This command displays new journal entries for Nginx as they arrive.

**When do we use it:**

When reproducing an error and observing what the service records at that moment.

**Real-time scenario:**

You are troubleshooting a website issue. You follow the service logs while testing the website to see whether new log entries reveal a problem.

Press Ctrl+C to stop following the logs.

### journalctl -b — View Logs from the Current Boot

**Command:**

```bash
journalctl -b
```

**Simple explanation:**

This command displays journal entries from the current system boot.

**When do we use it:**

When investigating problems that happened after the server started.

**Real-time scenario:**

A Linux server restarted, and an application failed to come up. You inspect the current boot's logs for errors that occurred during startup.

### journalctl -p err -b — Find Error Messages

**Command:**

```bash
journalctl -p err -b
```

**Simple explanation:**

This command displays current-boot journal entries at error priority or higher severity.

**When do we use it:**

When you want to narrow your investigation to important error messages rather than reviewing every log entry.

**Real-time scenario:**

Several services are behaving unexpectedly after a reboot. You use this command to find relevant error messages.

### journalctl -u jenkins --since today — View Today's Jenkins Logs

**Command:**

```bash
sudo journalctl -u jenkins --since today
```

**Simple explanation:**

This command displays available Jenkins journal entries from today.

**When do we use it:**

When investigating an issue that began today and you want to focus on recent service activity.

**Real-time scenario:**

A Jenkins build agent stopped working this morning. You inspect today's Jenkins logs for relevant errors.

### dmesg -T — Check Kernel Messages

**Command:**

```bash
dmesg -T
```

**Simple explanation:**

This command displays messages from the Linux kernel, including certain hardware, driver and system events.

**When do we use it:**

When investigating low-level system problems, such as device detection issues or some memory-related events.

**Real-time scenario:**

A storage device is not behaving as expected. You inspect kernel messages for clues about device detection or I/O errors.

Access to some messages may require elevated permissions.

### tail -n 50 /var/log/syslog — Read Recent System Logs

**Command:**

```bash
sudo tail -n 50 /var/log/syslog
```

**Simple explanation:**

This command displays the last 50 lines of the system log file, if the file exists.

**When do we use it:**

When investigating recent system events recorded in that file.

**Real-time scenario:**

You are troubleshooting an application on a Linux distribution that records system messages in /var/log/syslog. You inspect the most recent entries for relevant errors.

Log locations vary by Linux distribution.

### tail -f /var/log/syslog — Watch a Log File Live

**Command:**

```bash
sudo tail -f /var/log/syslog
```

**Simple explanation:**

This command displays new lines as they are added to a log file.

**When do we use it:**

When you want to watch a log while reproducing an issue.

**Real-time scenario:**

You trigger an application operation and watch the log file to see whether new errors appear.

Press Ctrl+C to stop.

### grep -i error /var/log/syslog — Search for Errors

**Command:**

```bash
sudo grep -i error /var/log/syslog
```

**Simple explanation:**

This command searches the specified log file for lines containing the word error, without distinguishing uppercase and lowercase letters.

**When do we use it:**

When a log file is large and you want to find messages that may be relevant to a problem.

**Real-time scenario:**

Your application is failing, and the system log contains thousands of lines. You search for messages containing error to narrow the investigation.

Not every error message indicates the root cause, and relevant errors may use different wording.

### last — Check Recorded Login Sessions

**Command:**

```bash
last
```

**Simple explanation:**

This command displays recorded login sessions from the system's login history.

**When do we use it:**

When reviewing when users logged in or investigating recorded login activity.

**Real-time scenario:**

You are investigating a server incident and need to review recorded login sessions around the time of the incident.

### who — Check Currently Logged-In Users

**Command:**

```bash
who
```

**Simple explanation:**

This command shows users who currently have login sessions recorded on the system.

**When do we use it:**

When checking which users have active login sessions.

**Real-time scenario:**

Before performing maintenance on a shared Linux server, you check whether other users are logged in.

## 🌐 Networking Commands

### ip addr — Check Network Interfaces and IP Addresses

**Command:**

```bash
ip addr
```

**Simple explanation:**

This command displays network interfaces and the IP addresses assigned to them.

A network interface is the part of the system used to communicate over a network.

**When do we use it:**

When investigating network connectivity or checking the IP address assigned to the server.

**Real-time scenario:**

An application server cannot communicate with another system. You check its network interfaces and IP addresses before investigating further.

### ip route — Check Network Routes

**Command:**

```bash
ip route
```

**Simple explanation:**

This command displays the routes Linux uses to decide where to send network traffic.

**When do we use it:**

When investigating routing problems or checking the default gateway.

**Real-time scenario:**

A server can communicate with other machines on its local network but cannot reach an external destination. You inspect its routing table as part of troubleshooting.

### ping -c 4 google.com — Test Basic Network Reachability

**Command:**

```bash
ping -c 4 google.com
```

**Simple explanation:**

This command sends four test messages to the destination and checks whether replies are received.

**When do we use it:**

When performing an initial connectivity check.

**Real-time scenario:**

Your Linux server cannot reach an external service. You use ping to test basic reachability to a hostname.

A failed ping does not necessarily mean the server is offline. Firewalls and network policies may block ICMP traffic.

### ss -tulpn — Check Listening Network Ports

**Command:**

```bash
sudo ss -tulpn
```

**Simple explanation:**

This command lists listening TCP and UDP sockets and, where permissions allow, the associated processes.

A listening port is a network endpoint waiting for connections.

**When do we use it:**

When a website or application cannot be reached and you need to check whether the expected service is listening on a port.

**Real-time scenario:**

Your manager reports that a web application is unavailable. You use this command to check whether a service is listening on the expected port, such as 80 or 443.

A listening port alone does not prove that the application is working or accessible from outside the server.

### curl -I https://example.com — Check an HTTP Endpoint

**Command:**

```bash
curl -I https://example.com
```

**Simple explanation:**

This command sends an HTTP request and displays the response headers rather than the full page body.

**When do we use it:**

When checking whether a website or HTTP endpoint responds.

**Real-time scenario:**

Your application is deployed, but you are unsure whether its web endpoint responds. You use curl to inspect the HTTP response.

A successful response does not guarantee that every application feature is working.

### hostname — Check the Server Name

**Command:**

```bash
hostname
```

**Simple explanation:**

This command displays the system's hostname, which is its configured network name.

**When do we use it:**

When identifying which server you are connected to.

**Real-time scenario:**

You manage development, testing and production servers. You run hostname to help confirm which server you are working on before making changes.

### getent hosts example.com — Check Hostname Resolution

**Command:**

```bash
getent hosts example.com
```

**Simple explanation:**

This command asks the system's configured name-resolution services to look up the hostname.

Name resolution converts a hostname into address information.

**When do we use it:**

When an application cannot connect to a hostname and you suspect a name-resolution problem.

**Real-time scenario:**

An application cannot reach a database using its hostname. You use getent hosts to check whether the hostname resolves through the system's configured name-resolution mechanism.

## 🔎 Process Management Commands

### ps -p 1234 -o pid,ppid,cmd,%cpu,%mem — Inspect a Specific Process

**Command:**

```bash
ps -p 1234 -o pid,ppid,cmd,%cpu,%mem
```

**Simple explanation:**

This command displays selected details about the process with ID 1234.

Replace 1234 with the actual process ID.

**When do we use it:**

When you have already identified a suspicious process and want to inspect its command and resource usage.

**Real-time scenario:**

You find a process consuming high CPU in top. You use this command to inspect that specific process before deciding what to investigate next.

### pgrep nginx — Find a Process by Name

**Command:**

```bash
pgrep nginx
```

**Simple explanation:**

This command searches for process IDs matching the process name nginx.

**When do we use it:**

When checking whether a process with a particular name is running.

**Real-time scenario:**

You want to identify Nginx process IDs before inspecting their details.

If there is no match, the command may return no output.

### pstree -p — View Process Relationships

**Command:**

```bash
pstree -p
```

**Simple explanation:**

This command displays running processes in a tree structure, showing how processes are related to one another.

**When do we use it:**

When investigating which process started another process or understanding parent-child relationships.

**Real-time scenario:**

A script launches background processes unexpectedly. You use pstree -p to help understand the process relationships.

The pstree command may require installation.

### kill -15 1234 — Request a Graceful Process Shutdown

**Command:**

```bash
kill -15 1234
```

**Simple explanation:**

This command sends SIGTERM to process 1234, asking it to terminate gracefully.

Replace 1234 with the correct process ID.

**When do we use it:**

When a process needs to stop and you have confirmed that terminating it is appropriate.

**Real-time scenario:**

A test process is no longer needed. You identify its PID and send SIGTERM so that it can shut down cleanly.

Do not terminate production processes without understanding their role and impact.

### kill -9 1234 — Force a Process to Stop

**Command:**

```bash
kill -9 1234
```

**Simple explanation:**

This command sends SIGKILL, which forces the operating system to terminate the process. The process cannot handle this signal to perform normal cleanup.

**When do we use it:**

Only when a process must be terminated and a graceful shutdown has failed or is not possible.

**Real-time scenario:**

A non-critical test process is stuck and does not respond to SIGTERM. After confirming the correct PID and checking the impact, you may use SIGKILL as a last resort.

Avoid using it as the first troubleshooting step.

## 🛠️ General Linux Commands

### uname -a — Check Kernel and System Information

**Command:**

```bash
uname -a
```

**Simple explanation:**

This command displays information about the running kernel and system, including the kernel version and machine architecture.

**When do we use it:**

When checking which kernel is running or collecting system details for troubleshooting.

**Real-time scenario:**

You are investigating a compatibility issue and need to identify the kernel version running on the Linux server.

### date — Check System Date and Time

**Command:**

```bash
date
```

**Simple explanation:**

This command displays the system's current date and time.

**When do we use it:**

When investigating timestamps, scheduled jobs or log entries that appear to have unexpected times.

**Real-time scenario:**

An automated deployment appears to have run at an unexpected time. You check the server's date and time as part of the investigation.

### whoami — Check the Current User

**Command:**

```bash
whoami
```

**Simple explanation:**

This command displays the username associated with your current effective user identity.

**When do we use it:**

When checking which user you are operating as before running commands that may require specific permissions.

**Real-time scenario:**

You cannot access a protected directory. You run whoami to confirm your current user before investigating permissions.

### id — Check User and Group Information

**Command:**

```bash
id
```

**Simple explanation:**

This command displays your user ID, primary group ID and supplementary group memberships.

**When do we use it:**

When investigating permission problems or checking whether your account belongs to a required group.

**Real-time scenario:**

You cannot access a file that should be available to a particular group. You run id to inspect your group memberships.

### pwd — Check the Current Directory

**Command:**

```bash
pwd
```

**Simple explanation:**

This command displays the full path of your current working directory.

**When do we use it:**

When checking which directory you are in before running file-related commands.

**Real-time scenario:**

You are about to edit a configuration file or run a deployment script. You use pwd to verify your current directory first.

### ls -lah — List Files and Directories

**Command:**

```bash
ls -lah
```

**Simple explanation:**

This command lists files and directories, including hidden entries, with detailed information and readable file sizes.

**When do we use it:**

When checking whether a file exists or inspecting files in a directory.

**Real-time scenario:**

A deployment script cannot find a configuration file. You use ls -lah to inspect the directory and check whether the file exists.

### free -h && df -h — Check RAM and Disk Together

**Command:**

```bash
free -h && df -h
```

**Simple explanation:**

This command checks memory first and then checks disk space if the memory command succeeds.

The && operator runs the second command only if the first command succeeds.

**When do we use it:**

When performing a quick initial check of memory and disk usage during troubleshooting.

**Real-time scenario:**

An application is slow and uploads are failing. You check both memory and disk usage to help identify which resource needs further investigation.

### watch -n 2 'df -h; free -h' — Monitor Disk and RAM Continuously

**Command:**

```bash
watch -n 2 'df -h; free -h'
```

**Simple explanation:**

This command repeats both the disk and memory checks every two seconds.

**When do we use it:**

When observing how storage and memory usage change while an application runs.

**Real-time scenario:**

A large deployment is running, and you want to observe whether memory usage or disk consumption is increasing.

Press Ctrl+C to stop monitoring.

🚨 Important Linux DevOps Reminders

✅ df -h helps you identify a full filesystem — a full disk can cause application failures, but it does not automatically mean the entire server has crashed.

✅ systemctl enable nginx configures Nginx to start at boot — systemctl start nginx starts it now.

✅ top monitors CPU and memory usage — press P to sort by CPU usage and M to sort by memory usage.

✅ free -h shows memory usage — check the available memory rather than assuming all used RAM is a problem.

✅ du helps investigate directory sizes — df reports filesystem space usage.

✅ journalctl helps investigate service logs — read the error messages before deciding what to change.

✅ ss -tulpn helps you check listening ports — a listening port does not guarantee external connectivity.

✅ lsblk shows disks and partitions — do not format or partition a disk until you have verified its contents and purpose.

✅ kill -15 requests a graceful shutdown — use kill -9 only as a last resort.

🎯 How to Practise These Commands

Open your Linux virtual machine or terminal.

Practise a small group of commands each day.

Read the output and identify the important fields.

Connect each command to a troubleshooting scenario.

Record your own output and findings in your GitHub notes.

Never run destructive commands on production systems without understanding their impact.

Remember: A DevOps engineer does not simply memorize commands. The goal is to understand the problem, choose the appropriate command, interpret the output and take the correct action.
