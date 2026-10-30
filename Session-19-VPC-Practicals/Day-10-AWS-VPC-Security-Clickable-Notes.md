# Day 10 — AWS VPC Security and Networking

> **How to use these notes:** Click any topic in the table of contents to jump directly to its definition, why we use it, when we use it, where we use it, and examples.

## Table of Contents

1. [Security Group](#1-security-group)
2. [Network ACL (NACL)](#2-network-acl-nacl)
3. [Security Group vs NACL](#3-security-group-vs-nacl)
4. [Inbound Traffic](#4-inbound-traffic)
5. [Outbound Traffic](#5-outbound-traffic)
6. [VPN — Virtual Private Network](#6-vpn--virtual-private-network)
7. [VPC — Virtual Private Cloud](#7-vpc--virtual-private-cloud)
8. [VPC vs VPN](#8-vpc-vs-vpn)
9. [CIDR Block](#9-cidr-block)
10. [AWS Secrets Manager](#10-aws-secrets-manager)
11. [ACL — Access Control List](#11-acl--access-control-list)
12. [S3 Block Public Access (PAB)](#12-s3-block-public-access-pab)
13. [Amazon API Gateway](#13-amazon-api-gateway)
14. [VPC Peering](#14-vpc-peering)
15. [AWS PrivateLink](#15-aws-privatelink)
16. [Amazon VPC Lattice](#16-amazon-vpc-lattice)
17. [AWS Client VPN](#17-aws-client-vpn)
18. [Quick Revision Table](#18-quick-revision-table)
19. [Interview Questions](#19-interview-questions)
20. [Key Takeaways](#20-key-takeaways)

---

## 1. Security Group

### What is it?
A Security Group is a virtual firewall that controls inbound and outbound traffic for supported AWS resources, such as EC2 instances, through their network interfaces.

### Why do we use it?
We use Security Groups to allow only the network connections required by a resource.

### When do we use it?
Use a Security Group when you need to control connections to a server or another supported resource—for example, allowing HTTPS traffic to a web server or allowing an application server to connect to a database.

### Where do we use it?
Security Groups are associated with network interfaces of supported resources inside a VPC.

### Example
For a web server, you might allow:
- TCP port 443 from permitted client IP addresses for HTTPS.
- TCP port 22 only from a trusted administrative IP address, if SSH access is required.

### Important points
- Security Groups are **stateful**.
- They support allow rules, not explicit deny rules.
- They have inbound and outbound rules.
- Multiple Security Groups can be associated with a network interface, subject to AWS limits.
- Rules from associated Security Groups are combined.

[Back to Table of Contents](#table-of-contents)

---

## 2. Network ACL (NACL)

### What is it?
A Network Access Control List (NACL) is a network security control that allows or denies inbound and outbound traffic at the subnet level.

### Why do we use it?
We use NACLs to filter traffic entering or leaving a subnet and to apply additional subnet-level restrictions.

### When do we use it?
Use a NACL when you need subnet-level filtering, such as denying traffic from a particular IP address or IP range.

### Where do we use it?
NACLs are created in a VPC and associated with subnets.

### Example
A NACL can have a rule that denies traffic from a particular IPv4 address from reaching a subnet.

### Important points
- NACLs are **stateless**.
- They support both allow and deny rules.
- They have inbound and outbound rules.
- Rules are evaluated in ascending numerical order; the first matching rule is applied.
- Each subnet is associated with one NACL at a time.
- One NACL can be associated with multiple subnets.
- The default NACL allows all inbound and outbound traffic; a custom NACL denies traffic unless rules allow it.

[Back to Table of Contents](#table-of-contents)

---

## 3. Security Group vs NACL

### What is the difference?
Both are AWS network security controls, but they operate at different levels and handle connection traffic differently.

### Why do we use both?
Using both can provide layers of network security: Security Groups control traffic to resources, while NACLs filter traffic at the subnet boundary.

### When do we use them?
- Use Security Groups to control connections to individual resources.
- Use NACLs when subnet-level filtering or explicit deny rules are needed.

### Where are they applied?
- **Security Group:** Associated with a network interface.
- **NACL:** Associated with a subnet.

| Feature | Security Group | NACL |
|---|---|---|
| Level | Resource/network interface | Subnet |
| Connection tracking | Stateful | Stateless |
| Allow rules | Yes | Yes |
| Explicit deny rules | No | Yes |
| Inbound and outbound rules | Yes | Yes |
| Response traffic | Automatically permitted for an allowed connection | Must be permitted by the relevant rules in both directions |
| Association | Multiple Security Groups per network interface | One NACL per subnet at a time |

[Back to Table of Contents](#table-of-contents)

---

## 4. Inbound Traffic

### What is it?
Inbound traffic is network traffic entering a resource or network from another source.

### Why do we use inbound rules?
Inbound rules determine which incoming connections are permitted.

### When do we use it?
Use inbound rules when users, administrators, or other systems need to connect to a server or service.

### Where do we configure it?
Inbound rules are configured in Security Groups and NACLs.

### Example
When a browser sends an HTTPS request to an EC2-hosted website, that request is inbound traffic from the EC2 instance's perspective.

[Back to Table of Contents](#table-of-contents)

---

## 5. Outbound Traffic

### What is it?
Outbound traffic is network traffic leaving a resource or network and travelling to another destination.

### Why do we use outbound rules?
Outbound rules control which outgoing connections are permitted.

### When do we use it?
Use outbound rules when a server needs to download updates, call an external API, or communicate with another service.

### Where do we configure it?
Outbound rules are configured in Security Groups and NACLs.

### Example
When an EC2 instance downloads software updates from an external repository, the connection is outbound traffic from the EC2 instance's perspective.

### Important point
Security Groups automatically allow response traffic for an allowed connection. NACLs are stateless, so the rules must permit the required traffic in both directions.

[Back to Table of Contents](#table-of-contents)

---

## 6. VPN — Virtual Private Network

### What is it?
A VPN creates an encrypted connection over an existing network, such as the internet, so users or networks can communicate securely.

### Why do we use it?
We use a VPN to provide secure connectivity to private resources without necessarily exposing those resources directly to the public internet.

### When do we use it?
Use a VPN when remote employees need access to private company resources or when a company needs to connect its on-premises network to AWS.

### Where do we use it?
AWS provides:
- **AWS Client VPN:** For individual users connecting remotely.
- **AWS Site-to-Site VPN:** For connecting an on-premises network to an AWS VPC.

### Example
A DevOps engineer working from home can connect through AWS Client VPN and access a private EC2 instance if the required routes, authorization, and security rules allow it.

[Back to Table of Contents](#table-of-contents)

---

## 7. VPC — Virtual Private Cloud

### What is it?
An Amazon VPC is a logically isolated virtual network in AWS where we launch and manage resources.

### Why do we use it?
We use a VPC to organize our AWS network and control IP address ranges, subnets, routing, and network connectivity.

### When do we use it?
Use a VPC when deploying AWS resources such as web servers, application servers, and databases that need network connectivity.

### Where do we use it?
A VPC is created in an AWS Region. Subnets are created within the VPC in Availability Zones.

### Example
A VPC might contain:
- A public subnet for a web server.
- A private subnet for an application server.
- A private subnet for a database.

Routing and security controls are configured according to the application's requirements.

[Back to Table of Contents](#table-of-contents)

---

## 8. VPC vs VPN

### What is the difference?
A VPC is an isolated virtual network in AWS. A VPN is an encrypted connection over an existing network.

### Why do we use them?
- **VPC:** To create and manage an AWS network.
- **VPN:** To provide secure connectivity to a network or its resources.

### When do we use them?
Use a VPC when deploying AWS resources. Use a VPN when users or external networks need secure access to permitted resources.

### Where do we use them?
A VPC is created in AWS. A VPN can connect remote users or an external network to AWS or another network.

### Example
Think of a VPC as a private office building and a VPN as a secure tunnel that lets authorized employees reach it.

[Back to Table of Contents](#table-of-contents)

---

## 9. CIDR Block

### What is it?
CIDR stands for **Classless Inter-Domain Routing**. A CIDR block represents an IP address range using an IP address and a prefix length.

### Why do we use it?
We use CIDR blocks to define IP ranges for VPCs and subnets and to specify source or destination IP ranges in network rules.

### When do we use it?
Use CIDR when creating VPCs and subnets or when allowing or denying traffic from specific IP ranges.

### Where do we use it?
CIDR is used in VPC configuration, subnet configuration, route tables, Security Groups, and NACL rules.

### Examples
- `10.0.0.5/32` — exactly one IPv4 address.
- `10.0.0.0/24` — 256 total IPv4 addresses.
- `10.0.0.0/16` — 65,536 total IPv4 addresses.
- `0.0.0.0/0` — all IPv4 addresses.

### Important points
- `/32` identifies one IPv4 address.
- `0.0.0.0/0` covers all IPv4 addresses.
- Total address count is not always the same as the number of usable host addresses in an AWS subnet.

[Back to Table of Contents](#table-of-contents)

---

## 10. AWS Secrets Manager

### What is it?
AWS Secrets Manager is a service that securely stores and manages sensitive information, such as passwords, API keys, and database credentials.

### Why do we use it?
We use it to protect credentials instead of hardcoding them in application code or storing them in plain-text configuration files.

### When do we use it?
Use it when an application needs credentials to connect to a database or another service.

### Where do we use it?
Secrets Manager can be used by applications running on services such as EC2 and Lambda, with appropriate IAM permissions.

### Example
An application on EC2 needs to connect to an Amazon RDS database. The database credentials are stored in Secrets Manager, and the application retrieves them using an appropriately permissioned IAM role.

### Important points
- Provides secure storage and controlled retrieval of secrets.
- Supports access control through IAM.
- Supports automatic rotation for supported secrets and configurations.
- Helps reduce hardcoded credentials.

[Back to Table of Contents](#table-of-contents)

---

## 11. ACL — Access Control List

### What is it?
An Access Control List (ACL) is a set of permissions that controls access to a supported resource.

### Why do we use it?
We use ACLs to grant or restrict access to resources where the service supports ACL-based permissions.

### When do we use it?
Use an ACL when the relevant AWS service and use case require ACL-based access control.

### Where do we use it?
AWS uses ACLs in different contexts. For example, S3 ACLs can control access to S3 buckets and objects. Network ACLs control traffic at the subnet level.

### Example
An S3 object ACL may grant a particular AWS account permission to access that object.

### Important distinction
- **S3 ACL:** Controls access to S3 resources.
- **Network ACL (NACL):** Controls network traffic entering or leaving a subnet.

For most new S3 use cases, AWS recommends using policies and S3 Object Ownership with **Bucket owner enforced**, which disables ACLs.

[Back to Table of Contents](#table-of-contents)

---

## 12. S3 Block Public Access (PAB)

### What is it?
S3 Block Public Access is a group of settings that helps prevent Amazon S3 buckets and objects from becoming publicly accessible.

### Why do we use it?
We use it to reduce the risk of accidentally exposing files to the internet.

### When do we use it?
Use it when storing private files, backups, business documents, customer data, or other information that should not be publicly accessible.

### Where do we use it?
Block Public Access settings can be configured at supported levels, including the AWS account, bucket, and access point.

### Example
A company stores customer reports in an S3 bucket and enables appropriate Block Public Access settings to help prevent accidental public access.

### Important points
The four settings are:
- `BlockPublicAcls`
- `IgnorePublicAcls`
- `BlockPublicPolicy`
- `RestrictPublicBuckets`

These settings help prevent public access. Appropriate IAM permissions, bucket policies, and other relevant controls are still needed to manage access securely.

[Back to Table of Contents](#table-of-contents)

---

## 13. Amazon API Gateway

### What is it?
Amazon API Gateway is a managed AWS service used to create, publish, secure, monitor, and manage APIs.

### Why do we use it?
We use it to provide a controlled interface through which applications communicate with backend services.

### When do we use it?
Use it when a website, mobile application, or another service needs to access backend functionality through APIs.

### Where do we use it?
API Gateway is used in application architectures that connect clients to supported backend services, such as AWS Lambda or HTTP endpoints.

### Example
A mobile app requests customer information. API Gateway receives the request and routes it to a configured backend, which processes the request and returns a response.

### Important points
- Creates and manages APIs.
- Supports authentication and authorization mechanisms.
- Supports monitoring and API traffic management.
- Can integrate with supported AWS and external backend services.

[Back to Table of Contents](#table-of-contents)

---

## 14. VPC Peering

### What is it?
VPC Peering is a networking connection that allows two VPCs to communicate privately using their private IP addresses.

### Why do we use it?
We use VPC Peering when resources in separate VPCs need private network connectivity.

### When do we use it?
Use it when an application in one VPC needs to communicate with resources in another VPC.

### Where do we use it?
Create a peering connection between two VPCs and configure route tables in both VPCs to direct the required traffic through it.

### Example
- VPC-A contains an application server.
- VPC-B contains a database server.

A peering connection, appropriate routes, and security rules can allow the application server to connect to the database using its private IP address.

### Important points
- Provides private IP connectivity between two VPCs.
- The CIDR ranges must not overlap for standard VPC Peering.
- Both VPCs need appropriate routes.
- Security Groups and NACLs must allow the required traffic.
- VPC Peering does not provide automatic transitive routing.

[Back to Table of Contents](#table-of-contents)

---

## 15. AWS PrivateLink

### What is it?
AWS PrivateLink provides private connectivity between a VPC and supported services without requiring consumers to access those services through a public internet endpoint.

### Why do we use it?
We use it to access supported services privately and reduce the need to expose those services through public endpoints.

### When do we use it?
Use it when an application in one VPC needs private access to a service provided by another VPC, an AWS service, or a supported service provider.

### Where do we use it?
PrivateLink commonly uses interface VPC endpoints on the consumer side and endpoint services on the provider side.

### Example
Company A provides an application service. Company B creates an interface VPC endpoint to access that service privately from its VPC, subject to the service configuration and permissions.

### Important points
- Provides private service connectivity.
- Supports connectivity across VPCs and AWS accounts.
- Does not require the consumer to access the service through a public internet endpoint.
- Requires appropriate endpoint and service configuration.

[Back to Table of Contents](#table-of-contents)

---

## 16. Amazon VPC Lattice

### What is it?
Amazon VPC Lattice is a managed application networking service that connects, secures, and monitors communication between services.

### Why do we use it?
We use it to simplify service-to-service communication and apply service-level access controls across supported application environments.

### When do we use it?
Use it when an application has multiple services that need to communicate across VPCs, accounts, or supported compute environments.

### Where do we use it?
VPC Lattice connects supported application services through service networks and other VPC Lattice resources.

### Example
An e-commerce application has user, product, and order services running in different VPCs. VPC Lattice can help connect these services and apply service-level access controls and monitoring.

### Important points
- Connects application services.
- Supports service-level access controls.
- Helps monitor service communication.
- Is different from VPC Peering and AWS PrivateLink.

[Back to Table of Contents](#table-of-contents)

---

## 17. AWS Client VPN

### What is it?
AWS Client VPN is a managed VPN service that allows individual users to securely connect to AWS resources and other permitted networks.

### Why do we use it?
We use it to provide remote employees and administrators with secure network access.

### When do we use it?
Use it when users outside the company network need access to private AWS resources.

### Where do we use it?
Configure Client VPN in AWS and associate it with the relevant target networks, routes, authorization rules, and other required settings.

### Example
A DevOps engineer needs to access a private EC2 instance from home. The engineer connects through AWS Client VPN and can access the instance if the necessary authorization, routes, and security rules allow it.

### Important point
Client VPN provides network connectivity, but it does not automatically grant unrestricted access to the VPC.

[Back to Table of Contents](#table-of-contents)

---

## 18. Quick Revision Table

| Topic | Definition |
|---|---|
| [Security Group](#1-security-group) | A stateful firewall that controls traffic to and from supported resources. |
| [NACL](#2-network-acl-nacl) | A stateless network control that filters traffic at the subnet level. |
| [Inbound traffic](#4-inbound-traffic) | Traffic entering a resource or network. |
| [Outbound traffic](#5-outbound-traffic) | Traffic leaving a resource or network. |
| [VPN](#6-vpn--virtual-private-network) | An encrypted connection over an existing network. |
| [VPC](#7-vpc--virtual-private-cloud) | A logically isolated virtual network in AWS. |
| [CIDR](#9-cidr-block) | A notation used to represent IP address ranges. |
| [Secrets Manager](#10-aws-secrets-manager) | A service for securely storing and managing secrets. |
| [ACL](#11-acl--access-control-list) | A list of permissions controlling access to supported resources. |
| [S3 Block Public Access](#12-s3-block-public-access-pab) | Settings that help prevent public access to S3 data. |
| [API Gateway](#13-amazon-api-gateway) | A service for creating, securing, and managing APIs. |
| [VPC Peering](#14-vpc-peering) | A private network connection between two VPCs. |
| [AWS PrivateLink](#15-aws-privatelink) | Private connectivity to supported services. |
| [VPC Lattice](#16-amazon-vpc-lattice) | A service for connecting, securing, and monitoring application services. |
| [AWS Client VPN](#17-aws-client-vpn) | A service for secure remote network access. |

[Back to Table of Contents](#table-of-contents)

---

## 19. Interview Questions

1. What is a Security Group, and why do we use it?
2. What is a NACL, and where is it applied?
3. What is the difference between a Security Group and a NACL?
4. What is the difference between stateful and stateless?
5. What are inbound and outbound traffic?
6. What is the difference between a VPC and a VPN?
7. What does `0.0.0.0/0` mean?
8. What does `/32` mean in a CIDR block?
9. Can an EC2 instance have multiple Security Groups?
10. Can a subnet be associated with multiple NACLs simultaneously?
11. What is AWS Secrets Manager?
12. What is S3 Block Public Access?
13. What is VPC Peering?
14. What is AWS PrivateLink?
15. What is Amazon VPC Lattice?
16. What is AWS Client VPN?
17. What is Amazon API Gateway?

[Back to Table of Contents](#table-of-contents)

---

## 20. Key Takeaways

- Security Groups control traffic at the resource/network-interface level.
- NACLs control traffic at the subnet level.
- Security Groups are stateful; NACLs are stateless.
- Security Groups support allow rules only; NACLs support allow and deny rules.
- A subnet is associated with one NACL at a time.
- `0.0.0.0/0` represents all IPv4 addresses.
- An IPv4 CIDR prefix of `/32` identifies one IPv4 address.
- VPC Peering connects two VPCs privately.
- PrivateLink provides private connectivity to supported services.
- VPC Lattice helps connect and secure application services.
- Secrets Manager securely manages sensitive credentials.
- S3 Block Public Access helps prevent public exposure of S3 data.
- API Gateway manages application APIs.

[Back to Table of Contents](#table-of-contents)
