# AWS Day 7 --- Session 16 of 55: Databases, SQL, NoSQL, Amazon RDS & Aurora

> **Learning goal:** Understand databases in simple English, compare
> relational and non-relational databases, learn the purpose of Amazon
> RDS, and understand how to connect to a private RDS database securely
> from an EC2 jump host.

## Table of Contents

-   [1. What Is a Database?](#1-what-is-a-database)
-   [2. Why Do We Need a Database?](#2-why-do-we-need-a-database)
-   [3. What Is a DBMS?](#3-what-is-a-dbms)
-   [4. Main Types of Databases](#4-main-types-of-databases)
-   [5. Relational vs. Non-Relational
    Databases](#5-relational-vs-non-relational-databases)
-   [6. What Are SQL and NoSQL?](#6-what-are-sql-and-nosql)
-   [7. Database Examples: MySQL, PostgreSQL, Oracle, MongoDB and
    Cassandra](#7-database-examples-mysql-postgresql-oracle-mongodb-and-cassandra)
-   [8. What Is Amazon RDS?](#8-what-is-amazon-rds)
-   [9. Why Use RDS Instead of Installing a Database on
    EC2?](#9-why-use-rds-instead-of-installing-a-database-on-ec2)
-   [10. RDS Features](#10-rds-features)
-   [11. What Is Amazon Aurora?](#11-what-is-amazon-aurora)
-   [12. What Is a Database Endpoint?](#12-what-is-a-database-endpoint)
-   [13. RDS Networking: Public EC2 Jump Host and Private
    RDS](#13-rds-networking-public-ec2-jump-host-and-private-rds)
-   [14. What Happens If RDS Is Publicly
    Accessible?](#14-what-happens-if-rds-is-publicly-accessible)
-   [15. How to Protect an RDS
    Database](#15-how-to-protect-an-rds-database)
-   [16. Production Best Practices for
    RDS](#16-production-best-practices-for-rds)
-   [17. Creating an RDS Database](#17-creating-an-rds-database)
-   [18. Connecting to Private MySQL RDS Through an EC2 Jump
    Host](#18-connecting-to-private-mysql-rds-through-an-ec2-jump-host)
-   [19. Important Corrections and Interview
    Notes](#19-important-corrections-and-interview-notes)
-   [20. Quick Revision](#20-quick-revision)
-   [21. Interview Questions and
    Answers](#21-interview-questions-and-answers)

------------------------------------------------------------------------

## 1. What Is a Database?

A **database is an organized collection of data** that can be stored,
retrieved, updated, and managed when needed.

A web application often uses a database to store information such as
customer details, product information, orders, and transactions.

### Simple example

Imagine a machinery business. You use an application to manage your
customers and machines.

The database could store:

  Customer ID   Customer Name   Phone Number
  ------------- --------------- --------------
  101           Ahmed           9000000001
  102           Rahim           9000000002

When you enter a customer's information into the application, the
application sends it to the database. Later, the application can
retrieve that information from the database.

### How an application uses a database

**User → Application → Database**

1.  The user enters or requests information.
2.  The application processes the request.
3.  The application reads data from or writes data to the database.
4.  The application displays the result to the user.

**Remember:** - **Application:** The software users interact with to
perform tasks. - **Database:** The organized collection of data. -
**DBMS:** The software system used to create and manage databases.

A database does not have to be used only by a web application. Mobile
apps, desktop software, and backend services can also use databases.

## 2. Why Do We Need a Database?

Databases help applications manage information reliably and efficiently.

We use databases to:

1.  **Store data:** Save customer, product, order, and transaction
    details.
2.  **Retrieve data:** Find the information an application needs.
3.  **Insert data:** Add new records.
4.  **Update data:** Change existing records.
5.  **Delete data:** Remove records when permitted.
6.  **Organize data:** Keep information in a useful structure.
7.  **Connect related information:** For example, connect customers to
    their orders.
8.  **Control access:** Allow authorized users and applications to
    access data.
9.  **Support multiple users:** Let many users work with data according
    to the database's concurrency controls.
10. **Recover data:** Use backups and recovery features to help restore
    information after certain failures.

## 3. What Is a DBMS?

**DBMS** stands for **Database Management System**.

A DBMS is software that helps create, store, retrieve, update, and
manage databases. It can also provide features for permissions,
consistency, backup, and recovery.

Examples include MySQL, PostgreSQL, Oracle Database, and Microsoft SQL
Server.

In AWS, **Amazon RDS is a managed service** that helps you set up and
operate supported relational database engines. RDS is not itself a
single database engine.

## 4. Main Types of Databases

Common database categories include:

### 4.1 Relational databases

A relational database stores structured data in tables made of rows and
columns. Tables can be related using keys.

**Examples:** MySQL, PostgreSQL, Oracle Database, Microsoft SQL Server,
MariaDB, and Amazon Aurora.

**Common uses:** Banking systems, order management, inventory systems,
and applications that need structured records and reliable transactions.

### 4.2 Non-relational databases

Non-relational databases store data using models other than the
traditional relational-table model. Depending on the database, data may
be stored as documents, key-value pairs, graphs, or wide-column records.

**Examples:** MongoDB (document database), Amazon DynamoDB (key-value
and document database), and Apache Cassandra (wide-column database).

**Common uses:** Applications with flexible data structures, large-scale
distributed workloads, and use cases requiring particular access
patterns.

> Non-relational does not simply mean "unstructured data." NoSQL
> databases can store well-organized data, but they do not all use the
> same model.

### 4.3 Time-series databases

A time-series database is designed to store and query values associated
with timestamps.

**Example:** Monitoring a server's CPU usage every few seconds.

A record might contain a timestamp, server ID, and CPU usage percentage.
Time-series data is useful for metrics, sensor readings, and monitoring
history.

A time-series database is a category based on the workload and data
model; it is not the only database type that can store timestamped data.

## 5. Relational vs. Non-Relational Databases

  -----------------------------------------------------------------------
  Feature                 Relational database     Non-relational database
  ----------------------- ----------------------- -----------------------
  Common structure        Tables with rows and    Documents, key-value
                          columns                 pairs, graphs, or
                                                  wide-column records

  Schema                  Usually defined more    Often more flexible,
                          explicitly              depending on the
                                                  database

  Relationships           Commonly represented    Depends on the
                          with keys and joins     database; relationships
                                                  may be represented
                                                  differently

  Querying                Often uses SQL          Uses database-specific
                                                  query APIs or
                                                  languages; some support
                                                  SQL-like queries

  Examples                MySQL, PostgreSQL,      MongoDB, DynamoDB,
                          Oracle, Aurora          Cassandra

  Typical use             Orders, payments,       Flexible documents,
                          inventory, structured   key-value lookups,
                          business records        distributed or
                                                  high-volume workloads
  -----------------------------------------------------------------------

**Which one should we choose?** It depends on the application's data,
consistency requirements, access patterns, scale, and operational needs.
Neither type is automatically best for every application.

## 6. What Are SQL and NoSQL?

### SQL

**SQL** stands for **Structured Query Language**. It is commonly used to
create, read, update, and delete data in relational databases.

For example, a query to retrieve all customers from a table could look
like this:

``` sql
SELECT * FROM customers;
```

A query to add a customer could look like this:

``` sql
INSERT INTO customers (customer_id, customer_name)
VALUES (101, 'Ahmed');
```

Exact syntax and available features can vary between database engines.

### NoSQL

**NoSQL** is a broad term for database systems that do not rely solely
on the traditional relational-table model. Many NoSQL databases are
designed around specific data models and access patterns.

For example, MongoDB stores records as documents, while DynamoDB
supports key-value and document data models.

**Important:** SQL is a query language. NoSQL describes a family of
database approaches; it is not one single database product.

## 7. Database Examples: MySQL, PostgreSQL, Oracle, MongoDB and Cassandra

### 7.1 MySQL

MySQL is a widely used relational database management system. It is
commonly used for websites, business applications, and services that
store structured information.

-   Uses relational tables.
-   Commonly queried using SQL.
-   Has community and commercial offerings; licensing and support depend
    on the edition and distribution.
-   The default MySQL port is **3306**.

**Example use:** Store product details, customer accounts, and orders
for an online store.

### 7.2 PostgreSQL

PostgreSQL is a free, open-source object-relational database management
system.

-   Supports SQL and advanced database features.
-   Used for business applications, analytics workloads, and systems
    that need strong relational capabilities.
-   The default PostgreSQL port is **5432**.

**Example use:** Store and query customer orders with relationships
between customers, products, and transactions.

### 7.3 Oracle Database

Oracle Database is a relational database management system used in many
enterprise environments.

-   Offers enterprise features and commercial support.
-   Licensing and cost depend on the product, deployment, and agreement.
-   Common in large business systems, including some financial and
    enterprise applications.
-   The default Oracle Database listener port is commonly **1521**,
    although configurations can differ.

**Example use:** Support a large enterprise application that relies on
Oracle Database.

### 7.4 MongoDB

MongoDB is a NoSQL document database.

-   Stores records as documents, commonly represented using JSON-like
    structures (BSON internally).
-   Documents in a collection can have different fields, which provides
    schema flexibility.
-   Often useful for content, product catalogs, user profiles, and
    applications with evolving document structures.
-   Its default port is commonly **27017**.

Example document:

``` json
{
  "customer_id": 101,
  "name": "Ahmed",
  "city": "Hyderabad"
}
```

### 7.5 Apache Cassandra

Apache Cassandra is a distributed wide-column NoSQL database.

-   Designed for distributed data storage and high availability.
-   Can support high-volume read and write workloads when the data model
    and cluster are designed appropriately.
-   Often used for event data, large-scale operational data, and some
    IoT workloads.
-   Its default native transport port is commonly **9042**.

**Example use:** Collect and query large volumes of readings from
connected devices. A Wi-Fi connection alone does not make a device an
IoT device; IoT devices are physical devices that collect, send, or act
on data through connected systems.

### Quick comparison

  Database           Category     Typical data model                 Common default port
  ------------------ ------------ -------------------------------- ---------------------
  MySQL              Relational   Tables                                            3306
  PostgreSQL         Relational   Tables and relational features                    5432
  Oracle Database    Relational   Tables and relational features                    1521
  MongoDB            NoSQL        Documents                                        27017
  Apache Cassandra   NoSQL        Wide-column                                       9042

Default ports can be changed by configuration.

### MySQL vs. Oracle

Both are relational database management systems and use SQL, but they
differ in licensing, features, administration, tooling, and deployment
options.

Avoid saying that MySQL can have only one database or instance while
Oracle can have multiple databases. Database and instance architecture
differs between products and deployment configurations. For AWS RDS,
check the specific engine's supported configuration and terminology.

## 8. What Is Amazon RDS?

**Amazon RDS (Relational Database Service) is a managed AWS service that
makes it easier to set up, operate, and scale relational databases in
the cloud.**

RDS supports several database engines, including: - Amazon Aurora -
MySQL - PostgreSQL - MariaDB - Oracle - Microsoft SQL Server

Engine availability and supported versions depend on the current AWS
offering and Region.

### Why do we use Amazon RDS?

Suppose you need a MySQL database for an application. You have two broad
options:

1.  Create an EC2 instance and install and manage MySQL yourself.
2.  Use Amazon RDS to create a managed MySQL database.

With RDS, AWS manages many underlying administrative tasks, allowing the
cloud engineer to focus more on database configuration, access,
performance, security, and application needs.

RDS does **not** remove every database administration responsibility.
You still need to plan access, data, performance, backups, recovery,
monitoring, and costs.

## 9. Why Use RDS Instead of Installing a Database on EC2?

### Option 1: Install the database on EC2

You create an EC2 virtual machine, install a database engine, configure
it, and maintain it.

Depending on your design, you may need to manage: - Operating system
updates and patches. - Database software installation and patching. -
Backups and recovery testing. - High availability and replication. -
Storage capacity and scaling. - Monitoring and security configuration.

This approach gives you more control, but also more operational
responsibility.

### Option 2: Use Amazon RDS

AWS manages much of the database infrastructure and provides managed
features such as automated backups, maintenance options, monitoring
integrations, and Multi-AZ deployment options.

You still need to configure the database and its security correctly.

### Why RDS is useful

-   Reduces routine infrastructure administration.
-   Provides managed backup and recovery features.
-   Supports high-availability configurations.
-   Provides monitoring and performance tools.
-   Supports scaling options for supported engines and configurations.
-   Integrates with AWS networking, encryption, and access-control
    services.

**PaaS connection:** RDS is often described as a managed database
platform. You configure and use the database without manually managing
the underlying database server operating system in the same way you
would on EC2.

## 10. RDS Features

### Automated backups

RDS can automatically back up database data and transaction logs as part
of its recovery features. The configured backup retention period is
generally **0 to 35 days**, depending on the engine and configuration; a
retention period of 0 disables automated backups for supported
configurations. Check current engine-specific documentation.

Backups help with point-in-time recovery, subject to the engine and
configuration. Always test recovery procedures.

### Multi-AZ deployments

Multi-AZ options improve availability by maintaining database resources
across Availability Zones. The exact architecture and failover behavior
depend on the RDS deployment option.

**Important:** Multi-AZ is primarily for availability and failover, not
simply for increasing read capacity.

### Read replicas

Read replicas can help scale read-heavy workloads by serving supported
read queries. Their availability, replication behavior, and cross-Region
capabilities depend on the engine and configuration.

### Monitoring

RDS integrates with Amazon CloudWatch for metrics and alarms. Enhanced
Monitoring and Performance Insights or their current AWS equivalents may
provide additional visibility, depending on engine and offering.

### Encryption

RDS supports encryption at rest for supported configurations, using AWS
KMS. Encryption in transit can be enabled with TLS where supported and
properly configured.

### Maintenance and patching

AWS manages many maintenance tasks, but you must understand maintenance
windows, engine version upgrades, and any required application
compatibility testing.

### Scaling

RDS supports different scaling options, such as changing instance class,
storage settings, or using read replicas, depending on the engine and
configuration. Not every RDS database automatically scales every
resource.

## 11. What Is Amazon Aurora?

**Amazon Aurora is an AWS relational database engine compatible with
MySQL and PostgreSQL. It is available through Amazon RDS.**

Aurora is designed for high performance, availability, durability, and
managed operation. Its architecture separates compute from a distributed
storage layer.

### Why use Aurora?

-   MySQL or PostgreSQL compatibility.
-   Distributed storage architecture.
-   Managed backups and recovery features.
-   High-availability options.
-   Compute scaling options and storage that grows automatically as data
    is written, within service limits.

### Does Aurora automatically increase storage?

Aurora storage grows automatically as the database's data grows, within
the service's storage limits. Do not describe this as a universal rule
that Aurora waits until only 15--20% of storage remains. Aurora's
storage architecture differs from the provisioned storage model of many
other database configurations.

### Aurora vs. MySQL or PostgreSQL

MySQL and PostgreSQL are database engines that can run in different
environments, including self-managed servers and managed services.
Aurora is a separate AWS engine designed to be compatible with MySQL or
PostgreSQL.

Aurora is not the same thing as simply installing standard MySQL or
PostgreSQL on EC2.

## 12. What Is a Database Endpoint?

A **database endpoint** is a network address, usually a DNS hostname,
that a client uses to connect to a database.

For example, an RDS endpoint may look like this:

``` text
<your-db-instance>.<unique-id>.<region>.rds.amazonaws.com
```

The endpoint identifies the database connection destination. The
database client also needs the correct port, database name, username,
password or other authentication method, network connectivity, and
permission to connect.

**Security note:** Never publish a real database password in GitHub
notes. If a password has been shared in a chat, screenshot, repository,
or other place where it should not be exposed, rotate it immediately and
update any applications that use it. Do not store credentials in a
Markdown file.

## 13. RDS Networking: Public EC2 Jump Host and Private RDS

A common secure design keeps the database private and allows access from
a controlled EC2 instance or application server.

### Architecture

``` text
                         Internet
                            |
                     Internet Gateway
                            |
                 Public Subnet in a VPC
                 -----------------------
                 EC2 Jump Host / Bastion
                 Security Group: SSH only
                            |
                   Private network traffic
                            |
                 Private Subnet in a VPC
                 -----------------------
                      Amazon RDS
                 Security Group: MySQL 3306
                 Inbound source: Jump Host SG
```

This is a simplified diagram. A production environment may use a VPN,
AWS Systems Manager Session Manager, a private application server, or
other controlled access methods instead of a publicly reachable jump
host.

### Important AWS networking components

#### VPC

A **Virtual Private Cloud (VPC)** is a logically isolated network in AWS
where you place resources such as EC2 instances and RDS databases.

#### Availability Zone (AZ)

An Availability Zone is an isolated location within an AWS Region. A VPC
can span multiple Availability Zones.

#### Subnet

A subnet is a range of IP addresses in a VPC associated with one
Availability Zone.

-   **Public subnet:** Has a route to an Internet Gateway. A resource
    generally also needs a public IP and appropriate routing and
    security rules for direct internet connectivity.
-   **Private subnet:** Does not have a direct route from the internet
    to its resources. It is commonly used for databases and internal
    application services.

A subnet is not public merely because it is named "public"; its route
table and network configuration determine its routing.

#### Internet Gateway (IGW)

An Internet Gateway enables eligible resources in a VPC to communicate
with the internet when routing, IP addressing, and security settings
permit it. A public subnet commonly has a route such as `0.0.0.0/0` to
the IGW for IPv4.

#### NAT Gateway

A NAT Gateway lets resources in a private subnet initiate supported
outbound connections to the internet, such as for software updates, when
routing is configured. It does **not** provide unsolicited inbound
internet access to private resources.

**Correction:** An inbound connection to a public EC2 jump host
typically uses the Internet Gateway, not a NAT Gateway. A NAT Gateway is
not a substitute for an Internet Gateway.

#### Security Group

A Security Group is a stateful virtual firewall attached to resources
such as EC2 instances and RDS databases. It controls allowed inbound and
outbound traffic.

A common configuration is: - Jump host Security Group: allow SSH (TCP
22) only from a trusted IP or approved access path. - RDS Security
Group: allow MySQL (TCP 3306) only from the jump host's Security Group,
or from the application's Security Group if the application connects
directly.

Do not allow MySQL port 3306 from `0.0.0.0/0`.

#### Route table

A route table determines where network traffic from a subnet is
directed. Security Groups control permitted traffic; route tables
control traffic paths. Both matter, along with network ACLs, DNS, and
other network settings.

#### Jump host / bastion host / jump box

A jump host is a controlled server used as an access point to reach
resources in a private network. "Bastion host" and "jump box" are common
alternative names.

A **proxy server** is not automatically the same thing as a jump host. A
proxy forwards application traffic, while a jump host is commonly used
as a controlled administrative access point.

### How a connection reaches a private RDS database

1.  An authorized administrator connects to the EC2 jump host using the
    approved access method.
2.  The administrator runs a database client on the jump host or
    establishes a secure tunnel.
3.  The client connects to the private RDS endpoint on the database
    port, such as TCP 3306 for MySQL.
4.  The RDS Security Group checks whether the source is allowed.
5.  If networking, security rules, database credentials, and database
    permissions are correct, the connection succeeds.

In production, consider using AWS Systems Manager Session Manager or
private connectivity rather than exposing SSH to the whole internet.

## 14. What Happens If RDS Is Publicly Accessible?

When an RDS instance is configured as publicly accessible, it may
receive a publicly resolvable address and can potentially be reached
from the internet **if** network routing, Security Groups, and other
controls allow it.

Public accessibility does not automatically mean anyone can log in.
However, exposing a database unnecessarily increases the attack surface.

Potential risks include:

-   Attackers scanning for reachable database ports.
-   Automated bots repeatedly attempting usernames and passwords.
-   Exploitation of unpatched vulnerabilities or insecure
    configurations.
-   Attempts to abuse weak credentials or excessive permissions.
-   Regulatory or security-policy violations if the exposure breaks
    required controls.

### What is an automated bot?

An automated bot is a program that performs tasks automatically.
Attackers may use bots to scan IP addresses, identify open ports, and
repeatedly attempt logins. Bots can operate much faster and across more
systems than a person manually checking each one.

### What is SQL injection?

SQL injection is a vulnerability in which unsafe handling of user input
allows an attacker to alter a database query. Applications should use
parameterized queries, appropriate input handling, least-privilege
database accounts, and other secure development practices.

Making a database private does not by itself prevent SQL injection;
application security is also required.

### What is HIPAA?

HIPAA is a United States law that includes requirements for protecting
certain health information. Whether it applies depends on the
organization and data involved. Do not assume every database exposure
automatically constitutes a HIPAA violation; compliance depends on the
relevant facts and obligations.

## 15. How to Protect an RDS Database

1.  **Keep RDS private when possible.** Disable public accessibility
    unless a well-justified design requires it.
2.  **Restrict Security Groups.** Allow only approved application or
    administration resources to reach the database port.
3.  **Use least privilege.** Give each database user and AWS identity
    only the permissions it needs.
4.  **Encrypt data at rest.** Configure RDS encryption using AWS KMS
    where supported.
5.  **Encrypt data in transit.** Use TLS and validate certificates where
    supported.
6.  **Use AWS Secrets Manager.** Store and rotate database credentials
    where appropriate instead of committing passwords to files or
    GitHub.
7.  **Enable backups and test recovery.** A backup is useful only if
    recovery works when needed.
8.  **Monitor activity.** Use CloudWatch metrics and logs, database
    audit features where supported, and appropriate alerting.
9.  **Patch and maintain the database.** Plan upgrades and maintenance
    with application compatibility in mind.
10. **Use controlled administrative access.** Consider Systems Manager
    Session Manager, VPN, or a carefully secured bastion host.
11. **Protect the application.** Use parameterized queries and safe
    error handling to reduce SQL injection risk.
12. **Review access regularly.** Remove obsolete accounts, credentials,
    and network rules.

## 16. Production Best Practices for RDS

-   Use private subnets for databases where possible.
-   Restrict database Security Group inbound rules to specific
    application or access Security Groups.
-   Apply least privilege to database accounts and AWS IAM permissions.
-   Enable appropriate CloudWatch monitoring and alarms.
-   Store credentials in Secrets Manager or another approved
    secrets-management solution.
-   Enable encryption at rest and secure connections in transit.
-   Configure automated backups and select a suitable retention period.
-   Test point-in-time recovery and disaster recovery procedures.
-   Use Multi-AZ deployment options when availability requirements
    justify them.
-   Consider read replicas for supported read-scaling use cases.
-   Review performance, connection counts, storage, and costs.
-   Use maintenance windows and test major engine upgrades.
-   Do not put passwords, access keys, or other secrets in GitHub
    repositories.
-   Use a jump host only when necessary and secure it carefully; do not
    treat it as the only possible way to access private databases.
-   Document who can access the database and why.

## 17. Creating an RDS Database

The AWS Console labels and available options can change, but the general
workflow is:

1.  Sign in to the AWS Management Console.
2.  Open **Amazon RDS**.
3.  Choose **Create database**.
4.  Select a creation method, such as Standard create or Easy create,
    depending on the console options.
5.  Choose the database engine, such as MySQL or PostgreSQL, or an
    Aurora-compatible engine.
6.  Select an engine version supported in your Region.
7.  Choose a template appropriate to your workload, such as
    development/test or production.
8.  Configure the DB instance identifier.
9.  Configure the master username and a strong password, or the
    supported authentication option.
10. Choose an instance class and storage settings appropriate to the
    workload.
11. Select the VPC and DB subnet group.
12. For a private database, disable **Public access** and ensure the
    subnet group and routing are designed appropriately.
13. Configure the RDS Security Group to allow only required sources and
    ports.
14. Configure encryption, backups, maintenance, monitoring, and
    availability options.
15. Review the estimated cost and settings.
16. Choose **Create database**.
17. Wait until the database status is available.
18. Copy the endpoint and port from the database's Connectivity &
    security details.
19. Test connectivity from an authorized resource in the VPC.

### Important cost reminder

RDS can incur charges for database compute, storage, backups beyond
included allowances, data transfer, and other selected features. Review
the current pricing for your Region and engine before creating
resources, and delete test resources when they are no longer required.

## 18. Connecting to Private MySQL RDS Through an EC2 Jump Host

### Before you start

You need: - An EC2 jump host with a supported way to access it. - A
private MySQL RDS instance in a reachable VPC/network. - A jump host
Security Group that permits the approved administrative access method. -
An RDS Security Group allowing TCP 3306 from the jump host Security
Group. - The RDS endpoint, database name, and valid credentials. - A
MySQL client installed on the host you use to connect.

### Security Group example

**Jump host Security Group** - Inbound: SSH TCP 22 from your trusted
public IP, if using SSH directly. - Outbound: allow required traffic,
including database TCP 3306 as appropriate.

**RDS Security Group** - Inbound: MySQL/Aurora TCP 3306. - Source: the
jump host's Security Group ID, not `0.0.0.0/0`.

Use the least permissive rules that work for your setup.

### Example connection command

Run the following from the jump host if the MySQL client is installed:

``` bash
mysql -h <RDS-ENDPOINT> -P 3306 -u <DB-USERNAME> -p
```

Replace the placeholders with your endpoint and username. The `-p`
option prompts you for the password so it does not need to be typed
directly into the command.

If you need to select a particular database after connecting:

``` sql
SHOW DATABASES;
USE <DATABASE-NAME>;
SHOW TABLES;
```

The exact permissions determine which databases and tables you can see.

### Troubleshooting checklist

If the connection fails, check:

1.  Is the RDS status available?
2.  Is the endpoint correct?
3.  Is the client using the correct port?
4.  Is the RDS Security Group allowing TCP 3306 from the correct source
    Security Group?
5.  Is the EC2 instance in a network that can route to the RDS subnet?
6.  Are VPC, subnet, DNS, network ACL, and route settings correct?
7.  Is the username/password correct?
8.  Does the database user have permission to connect?
9.  Is TLS required by the database configuration?
10. Is a local firewall or other network control blocking the
    connection?

A timeout often points to a network or firewall path problem; an
authentication error usually points to credentials, authentication
settings, or database permissions. These are clues, not absolute rules.

## 19. Important Corrections and Interview Notes

-   A database is an organized collection of data. A DBMS is software
    that manages the database.
-   A relational database organizes data into tables, rows, and columns
    and can connect tables using keys.
-   SQL is a query language; NoSQL is a broad family of non-relational
    database systems.
-   Amazon RDS is a managed relational database service, not one
    database engine.
-   Aurora is a relational database engine compatible with MySQL and
    PostgreSQL, available through Amazon RDS.
-   MySQL's common default port is 3306; PostgreSQL's is 5432.
-   A public subnet normally routes internet-bound traffic through an
    Internet Gateway. A NAT Gateway is used for supported outbound
    connections from private subnets; it does not provide inbound
    internet access to private resources.
-   A Security Group is a stateful virtual firewall. A route table
    determines traffic routes; they do different jobs.
-   RDS being publicly accessible does not automatically let everyone
    log in. Network rules and authentication still matter, but public
    exposure should be avoided unless necessary.
-   Multi-AZ is primarily for availability and failover. Read replicas
    are a different feature commonly used to scale read workloads.
-   RDS backup retention settings depend on the engine and
    configuration. Check the current AWS documentation instead of
    memorizing a fixed range for every configuration.
-   Engine versions change over time. Always select a currently
    supported version in the AWS Console and verify the official
    documentation rather than relying on a tutorial's version number.
-   A jump host is an administrative access point. A proxy server has a
    different purpose, even though some architectures can combine roles.

## 20. Quick Revision

  -----------------------------------------------------------------------
  Term                                Simple definition
  ----------------------------------- -----------------------------------
  Database                            An organized collection of data

  DBMS                                Software that manages databases

  Relational database                 Stores structured data in related
                                      tables

  NoSQL database                      Uses a non-relational data model
                                      such as documents or key-value
                                      pairs

  SQL                                 A language commonly used to query
                                      relational databases

  MySQL                               A relational database management
                                      system

  PostgreSQL                          An open-source object-relational
                                      database management system

  Oracle Database                     An enterprise relational database
                                      management system

  MongoDB                             A document-oriented NoSQL database

  Cassandra                           A distributed wide-column NoSQL
                                      database

  Amazon RDS                          An AWS-managed service for
                                      relational databases

  Amazon Aurora                       An AWS relational database engine
                                      compatible with MySQL and
                                      PostgreSQL

  Endpoint                            The network hostname/address used
                                      by a client to connect to a
                                      database

  VPC                                 A logically isolated network in AWS

  Public subnet                       A subnet with a route to an
                                      Internet Gateway

  Private subnet                      A subnet without direct inbound
                                      internet routing to its resources

  Internet Gateway                    Enables eligible VPC resources to
                                      communicate with the internet when
                                      configured

  NAT Gateway                         Enables supported outbound internet
                                      connections from private subnets

  Security Group                      A stateful virtual firewall for AWS
                                      resources

  Jump host                           A controlled access point to reach
                                      private resources

  Multi-AZ                            An availability configuration
                                      spanning Availability Zones

  Read replica                        A replica that can help serve read
                                      workloads, depending on the engine

  Automated bot                       A program that performs tasks
                                      automatically

  SQL injection                       A vulnerability that lets unsafe
                                      input alter database queries
  -----------------------------------------------------------------------

## 21. Interview Questions and Answers

### Q1. What is a database?

A database is an organized collection of data that can be stored,
retrieved, updated, and managed by applications or users.

### Q2. What is the difference between a database and a DBMS?

A database is the collection of data. A DBMS is the software used to
create and manage that data.

### Q3. What is the difference between SQL and NoSQL?

SQL is a language commonly used with relational databases. NoSQL refers
to a family of non-relational databases that use models such as
documents, key-value pairs, graphs, or wide-column records.

### Q4. What is Amazon RDS?

Amazon RDS is a managed AWS service that simplifies setting up,
operating, and scaling supported relational database engines.

### Q5. Name some RDS database engines.

Amazon RDS supports engines such as MySQL, PostgreSQL, MariaDB, Oracle,
Microsoft SQL Server, and Amazon Aurora. Availability depends on the
current AWS offering and Region.

### Q6. Why use RDS instead of installing MySQL on EC2?

RDS reduces the need to manage the underlying database infrastructure
and provides managed features such as backups, maintenance options,
monitoring integrations, and high-availability configurations. With
MySQL on EC2, you have more direct control but must manage more of the
infrastructure yourself.

### Q7. What is Amazon Aurora?

Amazon Aurora is an AWS relational database engine compatible with MySQL
and PostgreSQL, designed for managed operation, performance,
availability, and durability.

### Q8. What is a database endpoint?

A database endpoint is a hostname or network address that a database
client uses to connect to a database.

### Q9. Why should an RDS database usually be private?

Keeping RDS private reduces direct internet exposure. Access can be
limited to approved application servers or administrative access paths
through network rules and authentication.

### Q10. How can an EC2 instance connect to private MySQL RDS?

Place the resources in a network configuration that allows private
connectivity, permit TCP 3306 from the EC2 Security Group in the RDS
Security Group, and connect to the RDS endpoint using valid credentials
and an appropriate MySQL client.

### Q11. What is the difference between an Internet Gateway and a NAT Gateway?

An Internet Gateway supports internet connectivity for eligible VPC
resources when correctly configured. A NAT Gateway allows supported
outbound connections from private subnets; it does not allow unsolicited
inbound connections to private resources.

### Q12. What is the difference between Multi-AZ and a read replica?

Multi-AZ is used primarily to improve availability and failover. Read
replicas are primarily used to serve read workloads and can help scale
reads, depending on the database engine.

### Q13. What is the default MySQL port?

The common default MySQL port is TCP 3306.

### Q14. What are important RDS security practices?

Keep the database private where possible, restrict Security Groups, use
least privilege, encrypt data at rest and in transit, store credentials
securely, enable monitoring, configure backups, and test recovery.

------------------------------------------------------------------------

**Session 16 of 55 --- AWS Day 7 complete.**

*Study tip: Practise explaining each topic in your own words, then draw
the private RDS + EC2 jump host architecture from memory. Never include
real passwords, access keys, or other secrets in your GitHub notes.*
