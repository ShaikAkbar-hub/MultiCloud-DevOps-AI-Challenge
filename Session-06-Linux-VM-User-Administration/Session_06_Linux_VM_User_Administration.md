# SESSION 06 — LINUX VM & USER ADMINISTRATION
55-Session Multi Cloud + DevOps with AI Program

Module: Linux Administration + GCP
Session: 06
Instructor: Vikas Ratnawat

## Table of Contents

- [1. Introduction](#1-introduction)
- [2. Linux Users](#2-linux-users)
- [3. Root User](#3-root-user)
- [4. Normal User](#4-normal-user)
- [5. System / Service Users](#5-system--service-users)
- [6. User ID (UID)](#6-user-id-uid)
- [7. Linux Groups](#7-linux-groups)
- [8. User Management](#8-user-management)
- [9. Group Management](#9-group-management)
- [10. Linux File Permissions](#10-linux-file-permissions)
- [11. File vs Directory Permissions](#11-file-vs-directory-permissions)
- [12. Chmod — Change File Permissions](#12-chmod--change-file-permissions)
- [13. Symbolic Chmod](#13-symbolic-chmod)
- [14. File Ownership](#14-file-ownership)
- [15. Chgrp](#15-chgrp)
- [16. VI Editor](#16-vi-editor)
- [17. Common Linux Commands](#17-common-linux-commands)
- [18. GCP VM Hands-on Practice](#18-gcp-vm-hands-on-practice)
- [19. DevOps Real-Time Relevance](#19-devops-real-time-relevance)
- [20. Real-Time Example](#20-real-time-example)
- [21. Important Security Practices](#21-important-security-practices)
- [22. Key Takeaways](#22-key-takeaways)
- [23. Command Cheat Sheet](#23-command-cheat-sheet)
- [24. Session 06 Summary](#24-session-06-summary)



## 1. INTRODUCTION

Linux is one of the most important operating systems used in Cloud and DevOps environments.

Linux is widely used for:
- Cloud virtual machines
- Web servers
- Application servers
- Docker hosts
- Kubernetes nodes
- Jenkins servers
- Monitoring servers
- Database servers
- CI/CD environments
- Automation and scripting

Understanding Linux users, groups, permissions, ownership and basic commands is essential for a DevOps Engineer.


## 2. LINUX USERS

Linux supports different types of users.

The commonly encountered users are:

## 1. Root User
## 2. Normal User
## 3. System/Service User


## 3. ROOT USER

The root user is the superuser in Linux.

Important points:
- Root UID is 0.
- Root has almost unrestricted access.
- Root can create and delete users.
- Root can install software.
- Root can modify system configuration.
- Root can change file ownership and permissions.
- Root can start and stop services.
- Root can access protected files.

Check the current user:

Command:
`whoami`

Check the root user's UID:

Command:
`id root`

Example output:
uid=0(root) gid=0(root) groups=0(root)

Why should we avoid direct root login?

Direct root access can be dangerous because root has almost unrestricted permissions.

In production environments:
- Use a normal user account.
- Use sudo when administrative privileges are required.
- Follow the Principle of Least Privilege.
- Avoid unnecessary direct root access.

Example:
`sudo systemctl restart nginx`


## 4. NORMAL USER

A normal user has limited permissions in Linux.

Important points:
- Normal users usually have a UID of 1000 or higher on many Linux distributions.
- A normal user can manage their own files.
- A normal user cannot normally modify protected system files.
- A normal user cannot install system-wide software without elevated privileges.
- A normal user can run applications and commands allowed by their permissions.

Check the current user:

Command:
`whoami`

Check user ID and groups:

Command:
`id`

Example:
uid=1001(devops) gid=1001(devops) groups=1001(devops)


## 5. SYSTEM / SERVICE USERS

System users are generally created for applications and services rather than for interactive human login.

Examples:
- nginx
- mysql
- www-data
- jenkins

Purpose:
A service should run with only the permissions it needs.

This follows the Principle of Least Privilege.

For example, a web server should not normally run with full root privileges.


## 6. USER ID (UID)

Every Linux user has a unique User ID called UID.

Important points:
- Root user has UID 0.
- Normal users commonly start from UID 1000 on many distributions.
- System/service users usually use lower UID values, depending on the Linux distribution.
- UID is used internally by Linux to identify users.

Useful command:

`id`

Example:
`id username`

Example:
`id devops`


## 7. LINUX GROUPS

Groups are used to organize users and manage permissions.

Instead of giving permissions individually to many users, we can create a group and assign permissions to that group.

Useful commands:

`groups`

`groups username`

`id username`

Example:
groups devops


## 8. USER MANAGEMENT

Linux provides commands to create, modify and delete users.

### 8.1 useradd

Create a user:

`sudo useradd devops`

Create a user with a home directory:

`sudo useradd -m devops`

### 8.2 passwd

Set or change a user's password:

`sudo passwd devops`

### 8.3 usermod

Modify an existing user.

Add a user to a group:

`sudo usermod -aG docker devops`

Meaning:
-a = append
-G = supplementary group

The -a option is important because it prevents removing the user from their existing supplementary groups.

### 8.4 userdel

Delete a user:

`sudo userdel devops`

Delete the user and their home directory:

`sudo userdel -r devops`


## 9. GROUP MANAGEMENT

Create a group:

`sudo groupadd devops`

Add a user to a group:

`sudo usermod -aG devops username`

Check group membership:

`groups username`

Check detailed user and group information:

`id username`

Delete a group:

`sudo groupdel devops`


## 10. LINUX FILE PERMISSIONS

Linux uses permissions to control who can read, write and execute files and directories.

There are three permission categories:

u = User/Owner
g = Group
o = Others

There are three basic permissions:

r = Read
w = Write
x = Execute

Example:

-rwxr-xr--

This can be understood as:

Owner: rwx
Group: r-x
Others: r--

Permission values:

Read    = 4
Write   = 2
Execute = 1

Common permission combinations:

7 = rwx
6 = rw-
5 = r-x
4 = r--
3 = -wx
2 = -w-
1 = --x
0 = ---

Check permissions:

`ls -l`

Example:
-rw-r--r-- 1 devops devops 100 file.txt

Here:
Owner = devops
Group = devops
Permissions = rw-r--r--


## 11. FILE VS DIRECTORY PERMISSIONS

For a file:
- Read means the user can view the file contents.
- Write means the user can modify the file.
- Execute means the user can execute the file as a program/script.

For a directory:
- Read means the user can list directory contents.
- Write means the user can create/delete entries in the directory.
- Execute means the user can access/traverse the directory.


## 12. CHMOD — CHANGE FILE PERMISSIONS

chmod is used to change file or directory permissions.

Numeric method:

`chmod 755 script.sh`

755 means:
Owner = rwx
Group = r-x
Others = r-x

Another common example:

`chmod 644 file.txt`

644 means:
Owner = rw-
Group = r--
Others = r--

Other common permissions:

755 = rwxr-xr-x
644 = rw-r--r--
700 = rwx------
600 = rw-------


## 13. SYMBOLIC CHMOD

Permissions can also be changed using symbolic notation.

Give execute permission to the owner:

`chmod u+x script.sh`

Remove write permission from others:

`chmod o-w file.txt`

Give read permission to the group:

`chmod g+r file.txt`


## 14. FILE OWNERSHIP

Every file and directory has an owner and a group.

Check ownership:

`ls -l`

Change the owner:

`sudo chown username file.txt`

Change owner and group:

`sudo chown username:groupname file.txt`

Example:

`sudo chown devops:devops application.log`


## 15. CHGRP

chgrp is used to change the group ownership of a file or directory.

Example:

`sudo chgrp devops application.log`


## 16. VI EDITOR

VI is a commonly used text editor in Linux.

Important commands:

`i`
- Enter insert mode.

`Esc`
- Exit insert mode.

`:wq`
- Save and exit.

`:q!`
- Exit without saving.

`:w`
- Save the file.

Basic workflow:

## 1. Open a file:
`vi filename`

## 2. Press i to enter insert mode.

## 3. Type or edit content.

## 4. Press Esc.

## 5. Type :wq

## 6. Press Enter.

Example:

`vi test.txt`


## 17. COMMON LINUX COMMANDS

`pwd`
- Shows the current working directory.

`ls`
- Lists files and directories.

`ls -l`
- Shows detailed information.

`ls -la`
- Shows hidden files with detailed information.

`cd`
- Changes directory.

`mkdir`
- Creates a directory.

`touch`
- Creates an empty file.

`cp`
- Copies files or directories.

`mv`
- Moves or renames files.

`rm`
- Removes files.

`cat`
- Displays file contents.

`less`
- Views a file page by page.

`head`
- Displays the beginning of a file.

`tail`
- Displays the end of a file.

`tail -f`
- Continuously displays new lines added to a file.
- Commonly used for monitoring application logs.

`ps`
- Displays running processes.

`ps aux`
- Displays detailed process information.

`top`
- Displays running processes and system resource usage.

`df -h`
- Displays disk space usage in human-readable format.

`du -sh`
- Displays the size of a file or directory.

`find`
- Searches for files and directories.

`grep`
- Searches for matching text.

`grep -i`
- Searches without case sensitivity.

`whoami`
- Shows the current logged-in user.

`id`
- Shows UID, GID and group information.

`who`
- Shows currently logged-in users.

`hostname`
- Displays the system hostname.


## 18. GCP VM HANDS-ON PRACTICE

In this session, practical work was performed using Google Cloud Platform.

Activities included:

## 1. Opened the GCP Console.
## 2. Created or accessed a Linux virtual machine.
## 3. Used GCP Cloud Shell / terminal.
## 4. Connected to the Linux VM.
## 5. Practiced Linux commands.
## 6. Worked with Linux users.
## 7. Worked with groups.
## 8. Practiced file permissions.
## 9. Practiced file ownership.
## 10. Used the VI editor.

The purpose of this hands-on practice was to understand how Linux is administered on a cloud virtual machine.


## 19. DEVOPS REAL-TIME RELEVANCE

Linux administration is very important for DevOps engineers.

Linux is commonly used in:

AWS:
- EC2
- EKS worker nodes
- CI/CD servers
- Application servers

GCP:
- Compute Engine VMs
- GKE nodes
- Application servers

Azure:
- Linux Virtual Machines
- AKS nodes

DevOps tools and platforms:
- Jenkins
- Docker
- Kubernetes
- Ansible
- Terraform
- Prometheus
- Grafana
- ELK Stack

A DevOps engineer may need to:
- Create and manage users.
- Configure SSH access.
- Manage groups.
- Configure file permissions.
- Manage ownership.
- Check disk usage.
- Monitor processes.
- Analyze logs.
- Restart services.
- Troubleshoot applications.
- Automate administrative tasks.


## 20. REAL-TIME EXAMPLE

Suppose an application is deployed on a Linux server under:

`/opt/application`

A DevOps team can create a group:

`sudo groupadd devops`

Add a user:

`sudo usermod -aG devops username`

Change ownership:

`sudo chown -R root:devops /opt/application`

Set permissions:

`sudo chmod -R 775 /opt/application`

This allows controlled access to the application directory while avoiding unnecessary full root access.


## 21. IMPORTANT SECURITY PRACTICES

- Do not use root unnecessarily.
- Use sudo for administrative operations.
- Follow the Principle of Least Privilege.
- Use strong passwords.
- Give users only the permissions they need.
- Review file ownership and permissions.
- Avoid giving 777 permissions unless there is a specific and justified requirement.
- Monitor user access.
- Remove unused accounts.
- Secure SSH access.


## 22. KEY TAKEAWAYS

The major topics learned in Session 06 are:

- Linux user types
- Root user
- Normal user
- System/service users
- UID
- Linux groups
- User management
- Group management
- File permissions
- chmod
- File ownership
- chown
- chgrp
- VI editor
- Common Linux commands
- GCP Linux VM hands-on practice
- Linux administration in DevOps


## 23. COMMAND CHEAT SHEET

`whoami`
`id`
`id root`
`groups`
`groups username`

`sudo useradd username`
`sudo useradd -m username`
`sudo passwd username`
`sudo usermod -aG groupname username`
`sudo userdel username`
`sudo userdel -r username`

`sudo groupadd groupname`
`sudo groupdel groupname`

`ls -l`
`chmod 755 filename`
`chmod 644 filename`
`chmod u+x filename`

`sudo chown username filename`
`sudo chown username:groupname filename`
`sudo chgrp groupname filename`

`pwd`
`ls`
`ls -la`
`cd`
`mkdir`
`touch`
`cp`
`mv`
`rm`
`cat`
`less`
`head`
`tail`
`tail -f`
`ps`
`ps aux`
`top`
`df -h`
`du -sh`
`find`
`grep`
`who`
`hostname`

VI:
`i`
`Esc`
`:wq`
`:q!`
`:w`


## 24. SESSION 06 SUMMARY

Session 06 focused on Linux VM and User Administration.

The key learning flow was:

Linux Users
      ↓
Groups
      ↓
UID / GID
      ↓
File Ownership
      ↓
File Permissions
      ↓
User Administration
      ↓
Linux Commands
      ↓
GCP Linux VM Practice
      ↓
DevOps Infrastructure

Linux knowledge is a foundation for working with cloud infrastructure, containers, Kubernetes, CI/CD pipelines and production servers.


# SESSION 06 COMPLETE

Topics covered:
Linux VM
Linux Users
Root User
Normal Users
System Users
UID
Groups
User Management
Group Management
File Permissions
chmod
Ownership
chown
chgrp
VI Editor
Linux Commands
GCP VM Hands-on
DevOps Relevance
Security Best Practices
