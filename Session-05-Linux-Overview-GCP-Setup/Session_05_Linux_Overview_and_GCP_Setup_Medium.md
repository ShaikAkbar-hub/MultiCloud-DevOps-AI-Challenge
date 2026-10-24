# 🔥 Session 05 — Linux Overview & GCP Setup

## 📚 Topic
**Linux Overview & GCP Setup**

**Module:** Linux Administration for DevOps + GCP

---

## 💡 What I Learned

- Linux is widely used for servers, cloud environments, containers, and DevOps tools.
- **GCP Cloud Shell** is a browser-based terminal that provides a Linux environment to work with Google Cloud.
- Linux uses a hierarchical filesystem structure.
- Some important directories are:
  - `/` → Root of the entire filesystem
  - `/etc` → Configuration files
  - `/var/log` → System and application logs
  - `/home` → Home directories of regular users
  - `/tmp` → Temporary files
  - `/usr` → Applications, libraries, and utilities

---

## 🐧 What is Linux?

> **Linux is a free and open-source operating system widely used for servers, cloud infrastructure, containers, and DevOps.**

Linux provides a command-line interface (CLI), which allows administrators and DevOps engineers to manage systems using commands.

### Why is Linux important for DevOps?

- Most cloud platforms provide Linux-based virtual machines.
- Many DevOps tools run on Linux.
- It supports automation through shell scripting and command-line tools.
- It is flexible, stable, and highly customizable.

---

## 🧩 Linux Distributions

A **Linux distribution (distro)** is a packaged version of Linux that includes the Linux kernel, system tools, libraries, and a package manager.

### Examples

- Ubuntu
- Red Hat Enterprise Linux (RHEL)
- Amazon Linux
- Debian
- Rocky Linux

Different distributions may use different package managers and configuration methods.

For example:

```text
Ubuntu / Debian → apt
RHEL / Rocky → dnf
Amazon Linux → dnf
```

---

## 🏗️ Linux Architecture

Linux can be understood through the following layers:

```text
Applications
     ↓
Shell
     ↓
System Calls
     ↓
Kernel
     ↓
Hardware
```

### Kernel

The **kernel is the core of the Linux operating system**.

It manages system resources such as:

- CPU
- Memory
- Processes
- Storage
- Network
- Devices

### Shell

The **shell** allows users to interact with Linux by executing commands.

Examples:

- Bash
- Zsh
- Fish

### System Calls

System calls allow applications to request services from the Linux kernel.

---

## 📁 Linux Filesystem Hierarchy

The **Filesystem Hierarchy Standard (FHS)** provides a common structure for organizing files and directories in Linux.

| Directory | Purpose |
|---|---|
| `/` | Root of the filesystem |
| `/etc` | Configuration files |
| `/home` | User files |
| `/var/log` | System and application logs |
| `/tmp` | Temporary files |
| `/usr` | Applications and utilities |
| `/opt` | Optional application software |
| `/boot` | Files required for system boot |
| `/dev` | Device files |
| `/proc` | Process and kernel information |

### Example

If I want to check configuration files:

```bash
cd /etc
```

If I want to check logs:

```bash
cd /var/log
```

---

## 🪟 Linux vs Windows

| Linux | Windows |
|---|---|
| Open-source | Proprietary |
| Commonly used for servers and cloud | Commonly used for desktops and enterprise workloads |
| Strong command-line environment | Strong graphical interface |
| Uses `/` as the root directory | Uses drives such as `C:` |
| Many distributions are available | Different editions and versions are available |

### Simple conclusion

> **Linux is commonly used in cloud and DevOps environments because it provides strong command-line tools, flexibility, automation capabilities, and many server-focused distributions.**

---

# ☁️ GCP — Google Cloud Platform

## What is GCP?

**Google Cloud Platform (GCP)** is a cloud computing platform provided by Google.

It provides services such as:

- Compute
- Storage
- Networking
- Databases
- Security
- Monitoring
- Kubernetes
- AI and Machine Learning

### Simple definition

> **GCP provides computing resources and services over the internet, so organizations can use cloud infrastructure without maintaining all the physical hardware themselves.**

---

## 🖥️ What is GCP Cloud Shell?

**GCP Cloud Shell** is a browser-based command-line environment provided by Google Cloud.

It gives us a ready-to-use Linux terminal and Google Cloud tools without requiring a local Linux setup.

### Simple definition

> **Cloud Shell is a browser-based Linux terminal used to interact with and manage Google Cloud resources.**

Example:

```bash
gcloud --version
```

The `gcloud` command-line tool is used to interact with Google Cloud services.

---

## 💻 GCP Virtual Machine

A virtual machine (VM) is a software-based computer running on physical cloud infrastructure.

When creating a VM in GCP, we can configure:

### 1. Machine Configuration
Choose resources such as:

- CPU
- Memory
- Machine type

### 2. Operating System and Storage

Choose:

- OS image
- Boot disk
- Disk type
- Disk size

### 3. Networking

Configure options such as:

- VPC network
- Subnet
- IP address
- Firewall rules

### 4. Security and Access

Configure:

- User access
- Authentication
- Firewall rules
- Permissions

---

## 🛠️ Hands-on Tasks

Today I:

1. Set up a GCP account.
2. Explored the Google Cloud Console.
3. Explored GCP VM creation options.
4. Reviewed machine configuration, OS, storage, networking, and security settings.
5. Explored GCP Cloud Shell and its Linux-based terminal environment.

---

## 📝 Key Takeaways

- Linux is an important foundation for DevOps.
- The **kernel** manages system resources.
- The **shell** allows users to execute commands.
- Linux distributions include Ubuntu, RHEL, Amazon Linux, and Debian.
- `/etc` contains configuration files.
- `/var/log` commonly contains logs.
- GCP provides cloud computing services on demand.
- Cloud Shell provides a Linux terminal through a browser.
- GCP VMs can be configured with compute, storage, networking, and security settings.

---

## 🎯 My Learning Focus

I am focusing on Linux fundamentals because Linux is an important foundation for cloud infrastructure, Docker, Kubernetes, CI/CD, and other DevOps tools.
