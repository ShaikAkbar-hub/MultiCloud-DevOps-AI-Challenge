# Day 8 — AWS RDS and MySQL Connectivity

**Course:** Multi-Cloud + DevOps with AI  
**Day:** 8  
**Session:** 17 out of 55  
**Practical 7:** EC2 SQL Proxy (Jump Host) in a Public Subnet + Amazon RDS MySQL 8.4 in a Private Subnet

---

## Table of Contents

- [1. Practical Overview](#1-practical-overview)
- [2. What Is a Subnet?](#2-what-is-a-subnet)
- [3. Architecture and Request Flow](#3-architecture-and-request-flow)
- [4. Database Storage and Instance Size](#4-database-storage-and-instance-size)
- [5. Routing, Security Groups, and NAT Gateway](#5-routing-security-groups-and-nat-gateway)
- [6. Connect to RDS from EC2](#6-connect-to-rds-from-ec2)
- [7. Basic SQL Queries](#7-basic-sql-queries)
- [8. Key Linux Commands](#8-key-linux-commands)
- [9. Troubleshooting Checklist](#9-troubleshooting-checklist)
- [10. Minor Project 1 — Database Connectivity](#10-minor-project-1--database-connectivity)
- [11. Resume Project Description](#11-resume-project-description)
- [12. Important Security Notes](#12-important-security-notes)

---

## 1. Practical Overview

The objective of this practical is to connect to an **Amazon RDS for MySQL 8.4** database from an **EC2 instance** acting as a bastion host (jump host) in a public subnet.

The EC2 instance provides an administration point from which an authorized user can connect to the RDS database. The RDS database should remain in private subnets and should not be directly accessible from the public internet.

**Learning goals:**

- Understand public and private subnets.
- Understand how EC2 connects to Amazon RDS using its endpoint.
- Install and use the MySQL command-line client on EC2.
- Practice basic SQL commands.
- Configure security groups and routing appropriately.

> **Terminology:** A bastion host or jump host is an EC2 instance used as a controlled entry point for administration. It is not necessarily a SQL proxy. A SQL proxy is a distinct component that forwards database connections.

## 2. What Is a Subnet?

A **subnet** is a smaller logical range of IP addresses within a Virtual Private Cloud (VPC). Dividing a VPC address range into subnets helps organize resources and apply routing and security controls.

- **Public subnet:** A subnet whose route table has a route to an Internet Gateway (IGW). An EC2 instance generally also needs a public IPv4 address or Elastic IP and suitable security-group rules for direct internet connectivity.
- **Private subnet:** A subnet that does not have a direct route to an Internet Gateway. Resources can still communicate with permitted resources inside the VPC.

A subnet belongs to one Availability Zone.

## 3. Architecture and Request Flow

```text
Administrator
     |
     | SSH (port 22), restricted to a trusted source
     v
Internet
     |
Internet Gateway
     |
     v
VPC
 |
 +-- Public subnet
 |      |
 |      +-- EC2 bastion / administration host
 |             |
 |             | MySQL TCP connection (port 3306)
 |             v
 |
 +-- Private DB subnet(s)
        |
        +-- Amazon RDS for MySQL 8.4
               |
               +-- RDS endpoint (DNS hostname)
```

**Connection flow:**

1. The administrator connects to the EC2 bastion host using SSH.
2. The EC2 instance resolves the RDS endpoint using DNS.
3. The EC2 instance connects to the RDS database on TCP port `3306`.
4. RDS checks its security-group rules and database authentication.
5. The administrator runs SQL queries through the MySQL client.

The RDS endpoint is a **DNS hostname**, not a fixed IP address to rely on. Use the endpoint shown in the RDS console.

## 4. Database Storage and Instance Size

### Allocated database storage

The allocated storage is the disk capacity available to the RDS database. In the training environment, the database was described as having **512 GiB** allocated storage; verify the actual value in the RDS console because it depends on the configured instance and storage settings.

For supported RDS storage configurations, storage can often be increased, and storage autoscaling may be available if enabled and supported. Storage generally cannot be reduced in place through a simple setting change.

### DB instance size: CPU and memory

The DB instance class determines the database server's compute resources, including CPU and memory. The exact resources depend on the selected instance class.

For example, **16 GiB RAM and 16 vCPUs** may be available on some instance classes, but do not assume every database instance has those resources. Confirm the DB instance class and its specifications in the AWS console.

**Remember:**
- Storage = capacity for database files, logs, and related data.
- Memory (RAM) = working space for caching and query execution.
- CPU/vCPU = processing capacity for database operations.

## 5. Routing, Security Groups, and NAT Gateway

For EC2 to connect to RDS **within the same VPC**, a NAT Gateway is normally **not required**.

The usual requirements are:

1. EC2 and RDS have network paths to each other through the VPC's local routing.
2. The EC2 security group permits outbound database traffic (TCP `3306`) to the RDS security group, subject to the configured rules.
3. The RDS security group allows inbound TCP `3306` **from the EC2 security group**.
4. Network ACLs and DNS settings do not block the connection.

### What does a NAT Gateway do?

A NAT Gateway is placed in a **public subnet** and allows resources in private subnets to initiate outbound IPv4 connections to the internet, when the relevant route is configured. It is not a device that enables a public-subnet EC2 instance to connect to a private-subnet RDS database.

### Recommended security-group rules

**EC2 bastion security group**
- Inbound SSH `22`: only from your trusted public IP, or use AWS Systems Manager Session Manager instead.
- Outbound MySQL `3306`: to the RDS security group, if restricting outbound traffic.

**RDS security group**
- Inbound MySQL `3306`: source should be the EC2 bastion's security group.
- Do not open database port `3306` to `0.0.0.0/0`.

## 6. Connect to RDS from EC2

Commands below assume an Ubuntu EC2 instance and a MySQL RDS database.

### Step 1: Become root (optional)

```bash
sudo -i
```

This opens a root shell. You can instead prefix individual administrative commands with `sudo`.

### Step 2: Refresh package metadata

```bash
sudo apt update
```

This refreshes the local package index from configured repositories. It does **not** upgrade installed packages.

Run it when preparing to install software and during routine maintenance, rather than only after a maintenance event.

### Step 3: Upgrade installed packages (use care)

```bash
sudo apt upgrade
```

This upgrades installed packages where updates are available. It does not mean a package version will always increase by exactly one version (for example, from version 4 to 5). Review the proposed changes and follow your environment's maintenance policy.

### Step 4: Install the MySQL client

```bash
sudo apt install mysql-client
```

This installs the command-line client used to connect to a MySQL server. The exact package name can vary by Ubuntu release and repository; if unavailable, check the supported MySQL client package for your OS.

### Step 5: Verify the client

```bash
mysql --version
```

This prints the installed MySQL client version.

### Step 6: Connect using the RDS endpoint

```bash
mysql -h <RDS_ENDPOINT> -P 3306 -u admin -p
```

Replace `<RDS_ENDPOINT>` with the endpoint shown on the RDS console. For example:

```bash
mysql -h database-1.example.ap-south-1.rds.amazonaws.com -P 3306 -u admin -p
```

After you press Enter, the client prompts for the password. Type it when prompted; terminal input will not be displayed.

**Do not put the database password directly in the command.** It may be saved in shell history or exposed in process listings. If a password has already been shared or entered into a command, rotate it in RDS and use the secure prompt above.

### Step 7: Select the database

After connecting:

```sql
SHOW DATABASES;
```

Choose an existing database:

```sql
USE <database_name>;
```

If you have permission and need a practice database:

```sql
CREATE DATABASE practice_db;
USE practice_db;
```

## 7. Basic SQL Queries

Use these as practice queries after connecting. Run table-specific queries only after creating the corresponding table.

### 1. Display databases

```sql
SHOW DATABASES;
```

### 2. Create a practice database

```sql
CREATE DATABASE practice_db;
```

### 3. Select the database

```sql
USE practice_db;
```

### 4. Create a table

```sql
CREATE TABLE tutorials_tbl (
    tutorial_id INT NOT NULL AUTO_INCREMENT,
    tutorial_title VARCHAR(100) NOT NULL,
    tutorial_author VARCHAR(40) NOT NULL,
    submission_date DATE,
    PRIMARY KEY (tutorial_id)
);
```

### 5. Display tables

```sql
SHOW TABLES;
```

### 6. Insert sample records

```sql
INSERT INTO tutorials_tbl (tutorial_title, tutorial_author, submission_date)
VALUES
    ('MySQL Basics', 'Prashant', '2026-10-25'),
    ('AWS RDS', 'Akbar', '2026-10-25');
```

### 7. Read all records

```sql
SELECT * FROM tutorials_tbl;
```

### 8. Filter records

```sql
SELECT *
FROM tutorials_tbl
WHERE tutorial_author = 'Akbar';
```

### 9. Update a record

```sql
UPDATE tutorials_tbl
SET tutorial_title = 'AWS RDS and MySQL'
WHERE tutorial_id = 2;
```

### 10. Delete a record

```sql
DELETE FROM tutorials_tbl
WHERE tutorial_id = 2;
```

> **Caution:** `UPDATE` and `DELETE` should normally use a suitable `WHERE` clause. Without it, all rows may be affected.

### 11. Delete the practice table (optional)

```sql
DROP TABLE tutorials_tbl;
```

This permanently removes the table and its data. Do not run it on a table containing data you need.

## 8. Key Linux Commands

| Command | Purpose |
|---|---|
| `whoami` | Displays the current username |
| `sudo -i` | Opens a root shell |
| `sudo apt update` | Refreshes package metadata |
| `sudo apt upgrade` | Upgrades installed packages |
| `sudo apt install mysql-client` | Installs the MySQL client package, if available |
| `mysql --version` | Displays the client version |
| `exit` | Exits the MySQL session or current shell |

## 9. Troubleshooting Checklist

If the EC2 instance cannot connect to RDS, check:

1. **Endpoint:** Confirm you copied the RDS endpoint correctly.
2. **Port:** Use TCP `3306` for the default MySQL port unless the DB uses a custom port.
3. **RDS status:** Confirm the database is available.
4. **Security groups:** RDS inbound rules should allow port `3306` from the EC2 security group.
5. **Routing:** Confirm both resources have valid VPC routing between them.
6. **DNS:** Ensure VPC DNS resolution is enabled and the endpoint resolves.
7. **Credentials:** Verify the username and password; check that the user has appropriate permissions.
8. **Client installation:** Confirm the MySQL client is installed.
9. **Network ACLs:** Ensure they do not block the connection.

## 10. Minor Project 1 — Database Connectivity

**Project title:** Secure EC2-to-Amazon RDS MySQL Connectivity

**Objective:** Build a small AWS environment in which an EC2 administration host in a public subnet connects securely to an Amazon RDS for MySQL database in private subnets.

**Tasks completed / to document once verified:**
- Created or configured a VPC with public and private subnets.
- Launched an EC2 bastion host.
- Created an Amazon RDS for MySQL database.
- Configured security groups to allow database access from the EC2 security group.
- Installed the MySQL client on EC2.
- Connected to RDS using its endpoint.
- Executed SQL queries to create a database, create a table, insert data, read records, update records, and delete test records.

**Evidence to capture for a portfolio:**
- Architecture diagram (without passwords or secrets).
- VPC and subnet configuration.
- EC2 and RDS security-group rules.
- Successful MySQL connection with sensitive information hidden.
- SQL query results.

Only list tasks as completed after you have actually performed and verified them.

## 11. Resume Project Description

You can use the following wording after completing the project:

**Secure AWS Database Connectivity Using EC2 and Amazon RDS**
- Configured an EC2 bastion host in a public subnet to access an Amazon RDS for MySQL database in private subnets.
- Implemented security-group rules to restrict database access to the authorized EC2 security group.
- Installed and used the MySQL command-line client to connect through the RDS endpoint.
- Practiced SQL operations including database and table creation, inserts, queries, updates, and deletes.

**Resume accuracy note:** This is a learning project, not a substitute for professional employment experience. List it under **Projects** or **Hands-on Labs**, rather than presenting it as paid work or using it to claim employment tenure.

## 12. Important Security Notes

- A database password appeared in the original notes. Do not publish it in GitHub, LinkedIn, screenshots, or a public architecture diagram. If it is a real password, change it immediately.
- Use `-p` to enter the password at the prompt instead of including it in the command.
- Never expose the RDS database port publicly unless there is a specific, justified requirement and appropriate safeguards.
- Prefer AWS Systems Manager Session Manager where practical to reduce the need for inbound SSH.
- Use least-privilege database accounts for applications and practice; avoid using the administrative account for routine application access.
