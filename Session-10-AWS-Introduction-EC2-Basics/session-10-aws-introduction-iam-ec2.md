# Session 10 — AWS Introduction, IAM & EC2 Basics

**Program:** 55-Session Multi Cloud + DevOps with AI Program  
**Module:** AWS Top 10 Services + 10+ Practicals  
**Topic:** AWS Introduction & EC2 Basics  
**Live session:** Vikas Ratnawat

## Table of Contents

1. [Introduction to AWS](#1-introduction-to-aws)
2. [Why Use AWS?](#2-why-use-aws)
3. [Basic AWS Architecture](#3-basic-aws-architecture)
4. [AWS Regions and Availability Zones](#4-aws-regions-and-availability-zones)
5. [What Is Amazon EC2?](#5-what-is-amazon-ec2)
6. [What Is AWS IAM?](#6-what-is-aws-iam)
7. [Root User vs. IAM User](#7-root-user-vs-iam-user)
8. [IAM Users, Groups, Roles, and Policies](#8-iam-users-groups-roles-and-policies)
9. [Human Users and Applications Accessing AWS](#9-human-users-and-applications-accessing-aws)
10. [Create IAM Users and Groups in the AWS Console](#10-create-iam-users-and-groups-in-the-aws-console)
11. [Authentication and Authorization](#11-authentication-and-authorization)
12. [Elasticity and Scalability](#12-elasticity-and-scalability)
13. [High Availability](#13-high-availability)
14. [Key Takeaways](#14-key-takeaways)
15. [Quick Revision](#15-quick-revision)

---

## 1. Introduction to AWS

**Amazon Web Services (AWS)** is a cloud computing platform that provides on-demand services such as computing, storage, databases, networking, security, monitoring, and application deployment.

Instead of purchasing and maintaining all physical infrastructure yourself, you can provision cloud resources as needed and pay according to each service's pricing model.

Examples of AWS services:

- **Amazon EC2:** Virtual servers in the cloud.
- **Amazon S3:** Object storage.
- **Amazon VPC:** Logically isolated virtual networking.
- **Amazon RDS:** Managed relational databases.
- **AWS IAM:** Identity and access management.
- **Amazon CloudWatch:** Monitoring, metrics, logs, and alarms.
- **Elastic Load Balancing (ELB):** Distributes incoming traffic across targets.

AWS has a large and changing catalog of services. DevOps engineers should focus first on services required by their projects and roles rather than relying on a fixed service count.

## 2. Why Use AWS?

Organizations use AWS to build, deploy, and operate applications without having to own all the underlying physical infrastructure.

Key benefits include:

- **On-demand resources:** Provision infrastructure when needed.
- **Scalability:** Adjust capacity as application demand changes.
- **Global infrastructure:** Deploy workloads in supported AWS Regions.
- **Managed services:** Reduce operational effort for selected infrastructure components.
- **Automation:** Manage infrastructure using the console, CLI, SDKs, and infrastructure-as-code tools such as Terraform.
- **Security controls:** Use IAM, network controls, encryption, logging, and other security features.
- **Pay-as-you-go options:** Many services charge based on usage, though pricing varies by service and configuration.

AWS follows a shared responsibility model: AWS is responsible for security **of** the cloud, while customers are responsible for security **in** the cloud according to the services they use.

## 3. Basic AWS Architecture

Cloud application architecture describes how components such as users, networking, application servers, databases, and storage work together.

### Two-tier architecture

A two-tier architecture commonly separates an application into two layers:

1. **Presentation/application layer:** The user interface and/or application logic.
2. **Data layer:** The database or data store.

A simple example is an application running on an EC2 instance that connects to a database. Real implementations can vary.

### Three-tier architecture

A three-tier architecture separates an application into three logical layers:

1. **Presentation tier:** The user interface, often delivered through a web server or frontend.
2. **Application tier:** Business logic and application processing.
3. **Data tier:** Database and persistent data storage.

Example AWS design:

```text
Users
  |
Route 53 (DNS, optional)
  |
Application Load Balancer
  |
Web/Application tier (EC2 or containers)
  |
Database tier (for example, Amazon RDS)
```

This is an illustrative design. Production deployments may use multiple Availability Zones, private subnets, security groups, backups, monitoring, and separate web and application tiers.

## 4. AWS Regions, Zones, Availability Zones, and Edge Locations

### What is an AWS Region?

An **AWS Region** is a separate geographic area around the world where AWS operates cloud infrastructure. Each Region is designed to be independent from other Regions.

Examples of Regions include `us-east-1` (Northern Virginia) and `ap-south-1` (Mumbai). Region codes and availability should always be checked in the current AWS documentation.

### Why do we use Regions?

- **Geographic proximity:** Place workloads closer to users to help reduce network latency.
- **Data residency and compliance:** Choose a location that meets legal, regulatory, or organizational requirements.
- **Resilience and disaster recovery:** Use a separate Region as part of a multi-Region recovery strategy.
- **Service availability:** AWS services and features may not be available in every Region.
- **Cost planning:** Pricing can vary by Region, so compare costs before deploying.

A Region by itself does not guarantee that an application is highly available. The application must be designed and configured for resilience.

### What is an Availability Zone (AZ)?

An **Availability Zone (AZ)** is an isolated location within an AWS Region. An AZ consists of one or more data centers with independent infrastructure, designed so that failures in one AZ are less likely to affect another AZ.

Each Region has multiple AZs. AZs in the same Region are connected through high-bandwidth, low-latency networking, supporting architectures that distribute application components across AZs.

### Why do we use Availability Zones?

- **High availability:** Run application instances in multiple AZs so the application can continue operating if one AZ has an outage, provided the architecture and failover are configured correctly.
- **Fault isolation:** Reduce dependence on a single location.
- **Resilience:** Spread workloads and data across separate AZs where the service supports it.
- **Disaster planning within a Region:** Improve availability for certain AZ-level failures, while recognizing that a Region-wide event may still affect all AZs in that Region.

### What are AWS Zones?

In common AWS architecture discussions, people often say “zones” when they mean **Availability Zones**. The precise term is **Availability Zone (AZ)**. An AZ is not the same as a Region: a Region is a geographic area, and an AZ is an isolated location inside that Region.

### What is an AWS Edge Location?

An **AWS Edge Location** is a site in AWS's global edge network used by services such as Amazon CloudFront to deliver content and certain network services closer to end users. Edge locations are separate from the core AWS Region/AZ structure and are not the same as Availability Zones.

For example, CloudFront can cache static content such as images, CSS, JavaScript, and videos at edge locations. When a user requests that content, it may be served from a nearby edge location instead of fetching it from the origin every time.

### Why do we use Edge Locations?

- **Lower latency:** Serve content from a location closer to users.
- **Faster content delivery:** Cache frequently requested content near the audience.
- **Reduced load on the origin:** Cached content can reduce repeated requests to the origin server.
- **Global reach:** Improve delivery performance for users in different geographic areas.
- **Security and network services:** Some AWS edge services provide capabilities such as traffic acceleration or protection, depending on the service used.

An edge location does not automatically host your full application or database. It is commonly used by edge services to deliver or process supported traffic closer to users.

### Relationship between Regions, Availability Zones, and Edge Locations

```text
AWS Global Infrastructure
|
|-- AWS Region (for example, ap-south-1)
|   |
|   |-- Availability Zone A
|   |   |-- One or more data centers
|   |
|   |-- Availability Zone B
|   |   |-- One or more data centers
|   |
|   |-- Availability Zone C
|       |-- One or more data centers
|
|-- AWS Edge Locations
    |-- Help deliver cached content and supported edge services
        closer to end users
```

This diagram is conceptual, not a physical network map. The number of AZs and edge locations varies by Region and service.

### Quick comparison

| Concept | What it is | Main reason we use it |
|---|---|---|
| **Region** | A geographic area containing AWS infrastructure | Choose geography, meet residency needs, select available services, and plan disaster recovery |
| **Availability Zone (AZ)** | An isolated location within a Region | Improve resilience and high availability across locations |
| **Data center** | A physical facility that houses computing and networking equipment | Provides the underlying infrastructure; an AZ may contain one or more data centers |
| **Edge Location** | A site in AWS's global edge network used by services such as CloudFront | Deliver cached content and supported services closer to end users |

### Example: How they work together

Imagine an e-commerce application serving customers in India and other countries:

1. The application is deployed in an AWS Region selected for its requirements.
2. EC2 instances and other supported resources are distributed across multiple AZs to improve resilience.
3. A load balancer routes requests to healthy application instances.
4. Amazon CloudFront can cache static website content at edge locations closer to users around the world.
5. For disaster recovery, the organization may maintain a recovery environment in another Region if its recovery objectives require it.

**Remember:**
- **Region = geographic area**
- **Availability Zone = isolated location within a Region**
- **Data center = physical facility inside an AZ**
- **Edge Location = helps deliver supported content/services closer to users**

Do not assume that choosing a Region or using CloudFront alone makes the entire application highly available. Availability depends on the full architecture, service configuration, monitoring, and recovery design.

## 5. What Is Amazon EC2?

**Amazon Elastic Compute Cloud (Amazon EC2)** provides resizable compute capacity in the form of virtual servers called **instances**.

EC2 instances can run operating systems and applications such as web servers, backend services, CI/CD agents, and development environments.

Key concepts:

- **AMI (Amazon Machine Image):** A template used to launch an instance.
- **Instance type:** Defines compute resources such as vCPUs, memory, and networking capabilities.
- **EBS volume:** Persistent block storage that can be attached to supported EC2 instances.
- **Key pair:** A public/private key pair used for supported secure connection methods.
- **Security group:** A virtual firewall controlling allowed inbound and outbound traffic for associated resources.
- **VPC and subnet:** Networking constructs that determine where the instance is connected.
- **User data:** Optional startup instructions that can run during instance initialization.

Typical EC2 workflow:

1. Select an AMI.
2. Choose an instance type.
3. Select the VPC and subnet.
4. Configure a security group with only the required traffic.
5. Configure authentication and storage.
6. Launch the instance.
7. Connect securely and verify the application.

## 6. What Is AWS IAM?

**AWS Identity and Access Management (IAM)** helps control who or what can access AWS resources and which actions they are allowed to perform.

IAM helps manage identities and authorization for:

- Human users and workforce identities.
- Applications and workloads that need AWS access.
- AWS services acting through assigned roles.
- External identities federated into AWS.

IAM is a core part of AWS security. Follow the **principle of least privilege**: grant only the permissions required for a task, and review them regularly.

## 7. Root User vs. IAM User

### AWS account root user

The **root user** is the identity created when an AWS account is first established, using the email address and password associated with the account. It has extensive account-level privileges and should be protected carefully.

Best practices:

- Enable multi-factor authentication (MFA).
- Do not use the root user for routine administration.
- Do not create root access keys for everyday programmatic access.
- Use it only for tasks that specifically require root-user credentials.

### IAM user

An **IAM user** is an identity within an AWS account that can be given credentials and permissions. It may represent a person or, in some specific setups, an application identity.

An IAM user does **not** automatically have permissions to all AWS services. Its effective access depends on applicable policies and other access controls.

For workforce access, AWS generally recommends using federation and IAM Identity Center where appropriate instead of creating long-lived IAM users for every employee.

| Feature | Root user | IAM user |
|---|---|---|
| Identity | Account-level identity created with the account | Identity created within the account |
| Permissions | Extensive privileges | Depends on policies and applicable controls |
| Routine use | Avoid for everyday tasks | Use only when appropriate for the access model |
| Security | Protect with MFA and tightly limit use | Apply least privilege and secure credentials |
| Programmatic access | Avoid root access keys | Prefer temporary credentials or roles where possible |

## 8. IAM Users, Groups, Roles, and Policies

### IAM user

An identity that can authenticate using configured credentials. A user may receive permissions through policies attached directly to the user or through group membership.

### IAM group

A collection of IAM users. Permissions attached to a group apply to its member users. Groups simplify permission management but do not act as identities that log in or assume roles themselves.

Example: A `Developers` group could receive permissions required for development tasks.

### IAM role

An IAM role is an identity with a set of permissions that can be assumed by a trusted principal, such as an IAM user, an AWS service, or a federated identity. Roles commonly provide temporary credentials.

Examples:

- An EC2 instance role that lets an application read a specific S3 bucket.
- A deployment role assumed by an approved CI/CD pipeline.
- A cross-account role used by an authorized identity from another AWS account.

A role is not simply another name for a person or a fixed set of permissions; it combines permissions with a trust policy that defines who or what may assume it.

### IAM policy

A policy is a document, usually written in JSON, that defines permissions by specifying allowed or denied actions on resources, optionally under conditions.

In simple terms:

- **Policy:** What actions are allowed or denied?
- **Role:** Which trusted identity can assume this role, and what permissions does it receive?
- **Group:** Which IAM users share a set of permissions?
- **User:** Which identity is authenticating?

Policies can be attached to users, groups, or roles. AWS also evaluates other controls, such as explicit denies and applicable organization or resource policies, when determining effective access.

## 9. Human Users and Applications Accessing AWS

Humans and software can both interact with AWS, but the access method should suit the use case.

### Human access

A human may access the AWS Management Console through an approved workforce identity setup, such as IAM Identity Center, or through IAM user credentials where that method is appropriate.

Console access typically uses a username/password or federated sign-in, with MFA as required by the organization's security policy.

### Application and automation access

Tools such as the AWS CLI, SDKs, Terraform, and Jenkins can call AWS APIs to create or manage resources.

**Preferred approach:** Use IAM roles and temporary credentials where possible. For example, workloads running on EC2 can use an instance profile role rather than storing access keys in source code.

Access keys consist of an access key ID and a secret access key. They are for **programmatic access**, not for signing in to the AWS Management Console. Do not commit secret keys to GitHub, place them in plain-text scripts, or share them in screenshots.

For third-party systems, use an approved role-assumption or federation method where supported. If long-lived access keys are unavoidable, store them in a secrets manager, restrict permissions, rotate them, and monitor their use.

## 10. Create IAM Users and Groups in the AWS Console

The exact labels can change as the AWS console evolves. Use an appropriately authorized identity and follow least-privilege practices.

### Create a group

1. Sign in using an appropriately authorized identity.
2. Open the **IAM** service.
3. Select **User groups**.
4. Choose **Create group**.
5. Enter a group name, such as `Developers`.
6. Attach only the policies required for the group's tasks.
7. Review and create the group.

### Create an IAM user

1. In IAM, open **Users**.
2. Choose **Create user**.
3. Enter a user name.
4. Configure console access only if the person needs console sign-in and that method is appropriate for your organization.
5. Add the user to the relevant group when possible, or configure permissions according to your access model.
6. Review the configuration and create the user.
7. Configure credentials securely and require MFA according to organizational policy.

### Console access vs. programmatic access

- **Console access:** For a human who needs to use the AWS Management Console. Prefer approved workforce identity and federation options where available.
- **Programmatic access:** For CLI commands, SDKs, APIs, and automation. Prefer role-based temporary credentials rather than creating long-lived access keys.

Do not assume every IAM user needs both console and programmatic access. Grant only the access required for the job.

## 11. Authentication and Authorization

### Authentication

Authentication answers: **“Who are you?”**

Examples include signing in with approved credentials, federated identity, or other supported authentication methods.

### Authorization

Authorization answers: **“What are you allowed to do?”**

AWS evaluates applicable permissions to determine whether a requested action on a resource is allowed.

IAM helps manage identities and authorization policies. It is not accurate to say IAM alone provides every directory service or every authentication capability in an organization; workforce directories and federation may involve other AWS services or identity providers.

## 12. Elasticity and Scalability

These concepts are related but not identical.

### Elasticity

**Elasticity** is the ability to add and remove resources as demand changes, so capacity can better match workload needs.

Example: An Auto Scaling group adds EC2 instances during a traffic spike and removes instances when demand falls, within configured limits.

### Scalability

**Scalability** is the ability of a system to handle increased or decreased workload by changing available resources or system capacity.

#### Vertical scaling (scale up/down)

Increase or decrease the resources of an individual machine, such as moving to an EC2 instance type with more or fewer vCPUs and memory. This may require a restart or replacement, depending on the change.

#### Horizontal scaling (scale out/in)

Increase or decrease the number of machines or application instances.

Example: Add more EC2 instances behind a load balancer when traffic increases, then reduce the number when demand falls.

**Remember:** Elasticity describes how capacity adjusts to changing demand; scalability describes a system's ability to accommodate workload changes. Horizontal and vertical scaling are approaches to scaling.

## 13. High Availability

**High availability (HA)** is a system design goal that minimizes downtime and keeps an application accessible despite certain component failures.

For example, an application can run across multiple Availability Zones behind a load balancer, with health checks and appropriate failover. If one instance or AZ becomes unavailable, healthy components in another AZ may continue serving traffic.

Important points:

- Multiple servers in one location do not automatically provide high availability.
- Deploying across multiple AZs can protect against some AZ-level failures.
- Multi-Region architecture may be appropriate for stronger disaster recovery or geographic needs, but adds cost and complexity.
- Backups, monitoring, health checks, failover, and regular recovery testing are also important.
- High availability is not the same as elasticity: HA focuses on service continuity, while elasticity focuses on adjusting capacity to demand.

## 14. Key Takeaways

- AWS provides on-demand cloud services for compute, storage, networking, databases, security, and more.
- EC2 provides virtual servers; instance type determines compute resources.
- A Region is a geographic area; an Availability Zone is an isolated location within a Region.
- The root user has extensive privileges and should not be used for routine tasks.
- IAM users, groups, roles, and policies help organize identities and permissions.
- Policies define permissions; roles are assumed by trusted identities and commonly provide temporary credentials.
- Humans and applications both need secure access, but their authentication methods may differ.
- Use least privilege, MFA, and secure credential handling.
- Elasticity adjusts capacity as demand changes; vertical scaling changes machine size, and horizontal scaling changes instance count.
- High availability requires deliberate architecture and does not happen simply because an application runs on AWS.

## 15. Quick Revision

| Term | Simple definition |
|---|---|
| AWS | Cloud platform offering on-demand infrastructure and managed services |
| EC2 | Service for running virtual servers |
| Region | Geographic area containing AWS infrastructure |
| Availability Zone | Isolated location within an AWS Region |
| Data center | Physical facility containing computing and networking equipment |
| Edge Location | AWS edge-network site used by services such as CloudFront to deliver content closer to users |
| Root user | Account-level identity with extensive privileges |
| IAM user | Identity in an AWS account with configured credentials and permissions |
| IAM group | Collection of IAM users for shared permissions |
| IAM role | Assumable identity with permissions and a trust policy |
| IAM policy | Rules defining allowed or denied actions on resources |
| Authentication | Verifying who or what is requesting access |
| Authorization | Determining what the identity is allowed to do |
| Elasticity | Adjusting capacity as demand changes |
| Vertical scaling | Changing resources assigned to an individual machine |
| Horizontal scaling | Changing the number of machines or instances |
| High availability | Designing a system to minimize downtime during failures |

---

**Practice suggestion:** In a training or sandbox AWS account, create a least-privilege IAM group and test access with a non-production identity. Never experiment with root credentials or production permissions.
