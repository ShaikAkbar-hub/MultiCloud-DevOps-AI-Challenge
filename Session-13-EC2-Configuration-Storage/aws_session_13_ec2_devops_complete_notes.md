# Session 13/55 — AWS EC2: Beginner-Friendly DevOps Notes

**Program:** 55-Session Multi Cloud + DevOps with AI  
**Module:** AWS Top 10 Services + Practicals  
**Session:** 13 of 55

These notes explain each topic in simple language: **what it is, why we use it, where to find it, how it works or is created, and real-world DevOps use cases.**

## Table of Contents
1. [Amazon EC2](#1-amazon-ec2)
2. [EC2 Dashboard, Global View, and Events](#2-ec2-dashboard-global-view-and-events)
3. [Amazon Machine Image (AMI)](#3-amazon-machine-image-ami)
4. [Instance Types and Launch Templates](#4-instance-types-and-launch-templates)
5. [Amazon EBS](#5-amazon-elastic-block-store-ebs)
6. [EBS Volumes: Root and Additional Volumes](#6-ebs-volumes-root-and-additional-volumes)
7. [EBS Snapshots and Recovery](#7-ebs-snapshots-and-recovery)
8. [AMI vs Snapshot vs Volume](#8-ami-vs-snapshot-vs-volume)
9. [Networking and Security](#9-networking-and-security)
10. [Load Balancing](#10-load-balancing)
11. [Types of Load Balancers](#11-types-of-load-balancers)
12. [Target Groups and Health Checks](#12-target-groups-and-health-checks)
13. [Auto Scaling Groups](#13-auto-scaling-groups)
14. [Cost and Capacity Options](#14-cost-and-capacity-options)
15. [EBS Lifecycle Manager](#15-ebs-lifecycle-manager)
16. [Real-World DevOps Architecture](#16-real-world-devops-architecture)
17. [Interview Questions](#17-interview-questions)
18. [Quick Revision](#18-quick-revision)

---

## 1. Amazon EC2

### What is EC2?
**Amazon Elastic Compute Cloud (EC2)** is an AWS service that provides virtual servers in the cloud. These virtual servers are called **instances**. You choose an operating system, CPU/memory capacity, storage, and networking settings for your workload.

### Why do we use it?
- To host websites, APIs, backend services, and business applications.
- To run DevOps tools such as Jenkins or self-hosted CI runners.
- To create development, test, staging, and production environments.
- To run automation scripts and scheduled jobs.
- To increase or decrease compute capacity as requirements change.

### Where do we use it?
AWS Console → **EC2 → Instances**. EC2 can also be managed using AWS CLI, SDKs, CloudFormation, and Terraform.

### How do we create an instance?
1. Choose an AWS Region.
2. Open EC2 and choose **Launch instances**.
3. Select an AMI, such as Amazon Linux or Ubuntu.
4. Select an instance type.
5. Configure a key pair or an appropriate access method.
6. Choose a VPC, subnet, and security group.
7. Configure root and any additional storage.
8. Launch the instance and wait for it to become ready.
9. Connect using an appropriate method, such as SSH or Systems Manager Session Manager when configured.

### Real DevOps use case
A team needs a staging API server. The DevOps engineer provisions EC2, configures networking and access, deploys the application through a pipeline, and monitors it.

---

## 2. EC2 Dashboard, Global View, and Events

### 2.1 EC2 Dashboard
**What:** The central console page for EC2 resources and related features.  
**Why:** It provides shortcuts to instances, images, storage, networking, load balancing, and scaling.  
**Where:** AWS Console → EC2 → Dashboard.  
**DevOps use:** Quickly navigate to the resources needed for troubleshooting or infrastructure management.

### 2.2 AWS Global View
**What:** A view that helps you inspect selected EC2 resources across multiple AWS Regions.  
**Why:** Organizations may run workloads in more than one Region and need a consolidated view.  
**Where:** Use the Global View entry available in the EC2 console, if enabled for your account and console version.  
**DevOps use:** Check where EC2 resources are deployed without manually opening each Region. Global View does not mean every resource is global; many AWS resources remain Region-specific.

### 2.3 EC2 Events
**What:** Notifications or scheduled events related to EC2 resources, including events that may affect instances.  
**Why:** They help teams prepare for maintenance or investigate potential disruptions.  
**Where:** Review relevant EC2 event information and AWS Health views available to your account.  
**DevOps use:** If maintenance may affect an instance, review the event and plan a reboot, replacement, or other recommended action.

---

## 3. Amazon Machine Image (AMI)

### What is an AMI?
An **Amazon Machine Image (AMI)** is a template used to launch EC2 instances. It contains information needed to launch the instance, including an operating-system image and storage mappings. A custom AMI can capture an installed and configured software environment.

Examples include Amazon Linux, Ubuntu, and organization-created custom images.

### Why do we use an AMI?
- To launch servers with a consistent operating system and configuration.
- To avoid repeating the same software installation manually.
- To speed up server replacement and recovery.
- To standardize development, staging, and production environments.
- To provide a known image for Auto Scaling.

### Where do we find AMIs?
AWS Console → **EC2 → Images → AMIs**. The AMI Catalog helps you find available AWS-provided, Marketplace, or shared images.

### How do we create a custom AMI?
1. Launch an EC2 instance from a suitable base AMI.
2. Connect to it.
3. Install required software and dependencies.
4. Configure the operating system and required agents.
5. Remove temporary files and do not bake credentials or secrets into the image.
6. In EC2 → Instances, select the instance.
7. Choose **Actions → Image and templates → Create image**.
8. Enter an image name and create it.
9. Wait for the AMI to become available.
10. Launch a test instance from the new AMI and verify it.

A new instance launched from the AMI gets the software and configuration captured in the image. It may still need instance-specific settings, secrets, networking, host identity, or environment-specific application data.

### Real-world DevOps use cases
- **Golden image:** An approved base image with required updates, agents, and security settings.
- **Auto Scaling:** New instances launch with the same baseline.
- **Recovery:** Launch a replacement server from a known image.
- **Release management:** Test a new image before rolling it out.

**Important:** Creating an AMI does not change the image of an existing running instance. A common update strategy is to launch a replacement instance from the new AMI and migrate or reconnect the necessary data and configuration.

---

## 4. Instance Types and Launch Templates

### 4.1 EC2 instance types
**What:** An instance type defines a combination of compute resources, such as CPU, memory, network performance, and sometimes accelerators or storage characteristics.  
**Why:** Different workloads need different resources.  
**Where:** Choose an instance type during launch or in a launch template.  
**DevOps use:** Select a suitable type based on measured workload requirements, performance, availability, and cost.

### 4.2 Launch templates
**What:** A launch template stores reusable EC2 launch settings such as AMI ID, instance type, key pair, network settings, security groups, and storage configuration.  
**Why:** It makes launches consistent and reduces manual configuration.  
**Where:** EC2 → **Instances → Launch templates**.  
**How:** Create a template, use it to launch instances or configure an Auto Scaling Group, and create a new version when settings change.  
**DevOps use:** Update an approved AMI in a new launch-template version and roll out replacement instances through a controlled deployment process.

---

## 5. Amazon Elastic Block Store (EBS)

### What is EBS?
**Amazon Elastic Block Store (EBS)** provides persistent **block storage** for EC2 instances. Think of it as a virtual disk that can hold an operating system, applications, database files, or other data.

### Why do we use EBS?
- To store an EC2 operating system and applications.
- To provide persistent disk storage for data.
- To add capacity independently of the EC2 compute resources.
- To back up and restore disk data using snapshots.
- To choose storage capacity and performance suited to a workload.

### Where do we manage it?
AWS Console → **EC2 → Elastic Block Store → Volumes**. Snapshots and lifecycle policies have their own sections.

### What is block storage?
Block storage presents data as addressable blocks, similar to a disk attached to a computer. The operating system can format it with a filesystem and mount it at a directory.

- **EBS:** Virtual block disk for EC2.
- **S3:** Object storage accessed through APIs and object-based tools.
- **EFS:** Managed shared file storage for supported clients.

They serve different purposes and are not interchangeable in every scenario.

### Real-world DevOps use cases
- Root disk for a Linux server.
- Persistent data for tools such as Jenkins.
- Storage for a self-managed database when the design requires it.
- Extra disk capacity for builds, caches, or application data.
- Backups through snapshots with a defined retention policy.

---

## 6. EBS Volumes: Root and Additional Volumes

### What is an EBS volume?
An **EBS volume** is a virtual block-storage device. When attached to a compatible EC2 instance, the operating system can detect it. You may need to create a filesystem, mount it, and configure permissions before using it.

A volume is storage; it is not a complete server and does not provide CPU, memory, or an operating system by itself.

### Why is a volume created when EC2 launches?
For an **EBS-backed** instance, the AMI includes block-device mapping information that describes the storage to use. AWS typically creates a root EBS volume from that configuration so the instance has a bootable operating-system disk.

Additional volumes are created only if the image or launch configuration specifies them, or if an engineer creates them separately.

### What is a root volume?
The **root volume** is the disk containing the operating system used to boot an EBS-backed instance. It often also contains installed packages and application files unless they are stored elsewhere.

**Why is it needed?** The instance needs a bootable disk. The AMI's storage mapping defines the root device and its storage configuration.

### What is an additional/data volume?
An additional volume is a separate disk attached beyond the root volume. It can add capacity or separate application data from the operating system.

### How do we create and attach an additional volume?
1. Open AWS Console → EC2 → **Volumes**.
2. Choose **Create volume**.
3. Select a volume type, size, and performance settings suitable for the workload.
4. Select the Availability Zone.
5. Create the volume and wait until it is available.
6. Select it and choose **Attach volume**.
7. Select a compatible EC2 instance in the **same Availability Zone**.
8. Connect to the instance and identify the correct device using operating-system tools.
9. If the volume is empty, create a filesystem if required and mount it.
10. Configure persistent mounting and permissions if appropriate.

**Safety warning:** Device names shown in AWS and inside Linux can differ. Verify the correct disk before formatting it; formatting the wrong disk can destroy data.

### How is it used after attachment?
Attachment makes the block device available to the instance. On Linux, an engineer may need to create a filesystem and mount it at a directory such as `/data`. On Windows, the disk may need to be initialized and formatted. A newly attached volume does not necessarily appear as a ready-to-use directory automatically.

### Where can a volume be created?
An EBS volume is created in a specific **Availability Zone (AZ)** within an AWS Region. It can be attached to a compatible instance in that same AZ. To use its data in another AZ, create a snapshot and then create a new volume from that snapshot in the target AZ, subject to supported options.

### Why separate data from the root disk?
For suitable workloads, separation can make it easier to:
- Replace an instance without treating the data disk as disposable.
- Expand data storage independently.
- Apply separate backup and retention policies.
- Manage the operating system and data on different lifecycles.

It is not mandatory for every workload. Managed databases, S3, EFS, or other services may be better choices depending on the design.

### Real DevOps use cases
- **Jenkins:** Store important Jenkins state on persistent storage and back it up.
- **Self-managed database:** Use appropriately sized and tuned storage, plus tested backups.
- **Application files:** Keep data on persistent storage when the application architecture requires it.
- **Build jobs:** Provide disk space for build outputs and caches where appropriate.
- **Logs:** Store logs locally when needed, while using log rotation and centralized logging as appropriate.

### Volume deletion behavior
An EBS volume may be configured to be deleted when its EC2 instance is terminated, or it may be retained. This depends on the volume's **DeleteOnTermination** setting and launch configuration. Check the setting; do not assume every volume is automatically retained or deleted. Back up important data.

---

## 7. EBS Snapshots and Recovery

### What is an EBS snapshot?
An **EBS snapshot** is a point-in-time backup of an EBS volume. It can be used to create a new volume containing the captured data.

### Why do we use snapshots?
- Back up data on a volume.
- Restore data after accidental deletion or disk problems.
- Create a new volume from a known backup.
- Copy supported volume data to another AZ or Region.
- Support a recovery plan and retention requirements.

### Where do we find snapshots?
AWS Console → EC2 → **Elastic Block Store → Snapshots**.

### How do we create a snapshot?
1. Open EC2 → Volumes.
2. Select the volume.
3. Choose the action to create a snapshot.
4. Add a meaningful description and tags, such as application, environment, and date.
5. Create the snapshot and wait for completion.
6. Apply the required retention and access-control policies.

For application-consistent backups, especially for databases, coordinate with the application and follow the relevant backup procedure.

### How do we restore a volume?
1. Open EC2 → Snapshots.
2. Select the required snapshot.
3. Choose **Create volume from snapshot**.
4. Select the target AZ and appropriate volume settings.
5. Create the volume.
6. Attach it to a compatible EC2 instance in that same AZ.
7. Mount or access the filesystem and validate the restored data.

### What does a snapshot not restore automatically?
It captures volume data at a point in time. It does not automatically recreate all EC2 settings, such as security groups, instance type, IAM permissions, public IP address, load-balancer registration, or every application dependency. A snapshot is not automatically a complete application-consistent backup.

### Real DevOps example
A team schedules snapshots of an important data volume. During a recovery exercise, an engineer creates a new volume from a chosen snapshot, attaches it to a recovery instance, mounts it, and checks that expected files are present.

**Interview point:** A backup plan is not complete until restore procedures have been tested.

---

## 8. AMI vs Snapshot vs Volume

| Resource | Simple definition | Main purpose | Typical result |
|---|---|---|---|
| **AMI** | Template used to launch EC2 instances | Recreate a server's boot environment and configured storage layout | Launch a new EC2 instance |
| **EBS snapshot** | Point-in-time backup of an EBS volume | Back up and restore volume data | Create a new EBS volume |
| **EBS volume** | Persistent block storage | Store operating system, applications, or data | Attach a disk to a compatible EC2 instance |

**Easy memory trick:** AMI = server launch template; snapshot = volume backup; volume = virtual disk.

Creating an EBS-backed AMI uses snapshots of the backing EBS volumes, but an AMI and a snapshot are not the same thing.

---

## 9. Networking and Security

### 9.1 Security groups

**What:** Virtual firewalls for supported resources such as EC2 instances. They control allowed inbound and outbound traffic.  
**Why:** Limit access to the protocols, ports, and sources a workload actually needs.  
**Where:** EC2 → **Network & Security → Security Groups**, or the VPC console.  
**DevOps use:** Allow web traffic to a load balancer, then allow application traffic to EC2 servers only from the load balancer's security group.

**Security reminder:** Avoid opening SSH or other administrative ports to the whole internet without a specific, justified need and appropriate controls.

### 9.2 Key pairs

**What:** A public/private key pair. AWS can place the public key on a supported instance, while the user protects the private key.  
**Why:** Supports key-based authentication methods such as SSH to Linux instances.  
**Where:** EC2 → **Network & Security → Key Pairs**.  
**DevOps use:** Authorized engineers connect to Linux servers using protected SSH keys. Systems Manager Session Manager may be an alternative when configured.

### 9.3 Elastic IP addresses

**What:** A static public IPv4 address that can be allocated to an account and associated with supported resources.  
**Why:** Provides a stable public IPv4 address for certain designs.  
**Where:** EC2 → **Network & Security → Elastic IPs**.  
**DevOps use:** Some legacy or specialized designs need a stable public address. Many web architectures use a load balancer instead. Check current AWS pricing because charges can apply in some circumstances.

### 9.4 Placement groups

**What:** A feature that influences how EC2 instances are placed on underlying AWS hardware.  
**Why:** Different placement strategies can help with low-latency communication, failure isolation, or large distributed workloads.  
**Where:** EC2 → **Network & Security → Placement Groups**.  
**DevOps use:** A tightly coupled compute workload may use a suitable strategy after performance and availability requirements are assessed.

### 9.5 Network interfaces

**What:** An Elastic Network Interface (ENI) is a virtual network card in a VPC, with attributes such as private IP addresses and security groups.  
**Why:** Provides network connectivity and supports certain multi-interface designs.  
**Where:** EC2 → **Network & Security → Network Interfaces**.  
**DevOps use:** Troubleshoot IP addresses, subnet connectivity, and security-group associations.

### 9.6 VPC and subnets

**VPC:** A logically isolated network in AWS where IP ranges and network configuration are defined.  
**Subnet:** A range of IP addresses within a VPC; each subnet belongs to one Availability Zone.

**Why do they matter?** EC2 instances launch into a subnet in a VPC. Route tables, gateways, network ACLs, and security groups work together to determine connectivity.

**Real-world use:** Public-facing load balancers may be placed in public subnets, while application servers and databases can be placed in private subnets, depending on the architecture.

---

## 10. Load Balancing

### What is a load balancer?
A **load balancer** distributes incoming connections or requests across multiple targets, such as EC2 instances or other supported resources.

### Why do we use it?
- Distribute traffic across multiple servers.
- Reduce dependence on a single application server.
- Use health checks to avoid routing new traffic to targets marked unhealthy.
- Support scaling and controlled deployments.
- Provide a managed entry point for an application.

A load balancer alone does not guarantee high availability; the full architecture must also be resilient.

### Where do we create it?
AWS Console → EC2 → **Load Balancing → Load Balancers**, or the relevant Elastic Load Balancing service pages.

### How does it work?
1. A client sends a request to the load balancer endpoint.
2. A listener accepts traffic on a configured protocol and port.
3. Listener rules select a destination where supported.
4. Traffic is forwarded to eligible targets in a target group.
5. Health checks help determine target health.
6. The application processes the request and returns a response through the load balancer.

### Real DevOps use case
A web application runs on multiple EC2 instances. The load balancer distributes requests across healthy targets. If traffic grows, the team can add instances or use an Auto Scaling Group integration.

---

## 11. Types of Load Balancers

### 11.1 Application Load Balancer (ALB)
**What:** A load balancer designed for application-layer traffic, especially HTTP and HTTPS. It supports rules such as hostname- and path-based routing.  
**Why:** Route different web paths or hostnames to different services.  
**Where used:** Websites, APIs, and many microservices architectures.  
**Example:** Route `/api` to an API target group and `/shop` to a web target group.

### 11.2 Network Load Balancer (NLB)
**What:** A load balancer for supported transport-layer traffic, including TCP, UDP, and TLS.  
**Why:** Handle high-performance, low-latency connections and designs requiring supported static IP capabilities.  
**Where used:** TCP/UDP services and connection-heavy workloads.  
**Example:** Distribute TCP connections across registered service targets.

### 11.3 Gateway Load Balancer (GWLB)
**What:** Helps deploy and scale virtual network appliances, such as firewalls and traffic-inspection systems, by directing supported traffic through them.  
**Why:** Insert network appliances into traffic paths while supporting scaling and management.  
**Where used:** Centralized inspection and firewall architectures.  
**Example:** Direct selected traffic through virtual inspection appliances.

### 11.4 Classic Load Balancer (CLB)
**What:** An older-generation load balancer supporting basic Layer 4 and Layer 7 load balancing.  
**Why:** May still be used by existing legacy systems.  
**Where used:** Existing applications that depend on CLB. For new designs, evaluate ALB or NLB based on requirements.

| Type | Main traffic / layer | Common use |
|---|---|---|
| **ALB** | Layer 7, HTTP/HTTPS | Websites, APIs, host/path routing |
| **NLB** | Layer 4, TCP/UDP/TLS | High-performance connections |
| **GWLB** | Traffic steering through network appliances | Firewalls and inspection |
| **CLB** | Earlier-generation Layer 4/7 | Legacy systems |

**Interview tip:** Select based on protocol, routing features, performance, availability, and architecture—not simply because one type is newer.

---

## 12. Target Groups and Health Checks

### What is a target group?
A **target group** is a collection of targets to which a load balancer routes traffic. Depending on configuration, targets can include EC2 instances, IP addresses, or other supported target types.

### Why use it?
It defines the destination pool for a listener or listener rule and provides health-check configuration.

### Where is it?
EC2 → **Load Balancing → Target Groups**.

### What is a health check?
A health check tests whether a target appears able to receive traffic, using configured details such as protocol, port, path, and success criteria.

### Real DevOps use
The application must respond correctly on the configured health-check endpoint. A wrong path, blocked port, or slow startup can cause a healthy application to be marked unhealthy.

### Typical setup
1. Create a target group.
2. Configure target type, protocol, port, and health checks.
3. Register the EC2 instances or other targets.
4. Create a load balancer listener that routes to the target group.
5. Check target health and investigate application or network settings if targets are unhealthy.

---

## 13. Auto Scaling Groups (ASGs)

### What is an Auto Scaling Group?
An **Auto Scaling Group (ASG)** manages a group of EC2 instances and helps maintain a configured desired capacity. It can launch or terminate instances according to scaling policies and health checks.

### Why do we use it?
- Maintain the required number of application instances.
- Replace instances considered unhealthy by configured checks.
- Increase capacity when demand rises.
- Reduce capacity when demand falls, if scaling policies allow it.
- Spread instances across selected AZs for resilience.

### Where is it?
EC2 → **Auto Scaling → Auto Scaling Groups**.

### How do we set one up?
1. Prepare an AMI and/or launch template.
2. Create an Auto Scaling Group.
3. Select VPC subnets and Availability Zones.
4. Set minimum, desired, and maximum capacity.
5. Attach a load balancer target group if needed.
6. Configure health checks and scaling policies.
7. Monitor scaling activities, health, metrics, and costs.

### Minimum, desired, and maximum capacity
- **Minimum:** Lowest number of instances the group should maintain.
- **Desired:** Target number of instances the group aims to maintain.
- **Maximum:** Upper limit for the group's capacity.

### Real-world DevOps example
An online store normally runs three application instances. During a promotion, traffic increases. A scaling policy adds instances up to the configured maximum. The load balancer routes traffic to healthy targets. When traffic drops, the group can scale in.

**Design point:** Stateless application servers are often easier to scale horizontally. Keep important state in suitable shared or managed services rather than assuming data on one instance is available on all new instances.

---

## 14. Cost and Capacity Options

These options serve different purposes. Some reduce cost; others address capacity or dedicated infrastructure.

### 14.1 Spot Instances
**What:** Use spare EC2 capacity that AWS can interrupt.  
**Why:** Can substantially reduce cost for interruption-tolerant workloads.  
**Use cases:** Batch processing, flexible CI workers, and distributed jobs that can retry or resume.  
**Caution:** Design for interruptions; do not use Spot capacity for an interruption-intolerant workload without resilience.

### 14.2 Savings Plans
**What:** Pricing programs that can reduce eligible compute costs in exchange for a commitment to a consistent amount of usage over a term.  
**Why:** Potentially lower costs for steady or predictable usage.  
**DevOps use:** Review stable compute usage and evaluate whether a commitment fits.

### 14.3 Reserved Instances
**What:** A pricing option that can provide discounts for eligible EC2 usage in exchange for a commitment.  
**Why:** Reduce costs for predictable usage when the commitment fits.  
**DevOps use:** Review long-running workload usage and compare eligible pricing options.

### 14.4 Dedicated Hosts
**What:** Physical servers dedicated to your use.  
**Why:** Can support particular host-based licensing, compliance, or placement requirements.  
**DevOps use:** Consider when a workload has specific licensing or host requirements.

### 14.5 Capacity Reservations
**What:** Reserve EC2 capacity for supported instance attributes in a particular Availability Zone.  
**Why:** Help address capacity availability when specific instance capacity is required in a particular AZ.  
**DevOps use:** Evaluate for critical launches or workloads that require capacity assurance.

**Note:** Console features and availability can vary by account, Region, and current AWS offerings. Check current AWS documentation and pricing before making a commitment.

---

## 15. EBS Lifecycle Manager

### What is it?
**Amazon Data Lifecycle Manager (DLM)** can automate creation, retention, and deletion of supported EBS snapshots and EBS-backed AMIs according to lifecycle policies.

### Why use it?
- Reduce manual snapshot work.
- Follow a repeatable backup schedule.
- Retain backups according to policy.
- Remove eligible backups according to configured retention rules.

### Where is it?
In the EC2 console, look for **Elastic Block Store → Lifecycle Manager** or the relevant Data Lifecycle Manager interface.

### How is it used?
1. Select eligible resources, commonly through tags or supported resource selection.
2. Configure a schedule.
3. Define retention rules.
4. Review permissions and policy settings.
5. Monitor policy execution and periodically test restores.

### Real-world DevOps use case
A team tags eligible volumes and configures a lifecycle policy to create snapshots on a schedule. The team monitors the policy, checks that the intended volumes are covered, and tests restoration.

**Important:** Automated backups do not replace access controls, retention planning, monitoring, or restore testing.

---

## 16. Real-World DevOps Architecture

Imagine a company running a web application:

1. **AMI:** Prepared image with the operating system and required software or agents.
2. **Launch template:** Stores approved EC2 launch settings.
3. **Auto Scaling Group:** Maintains the desired number of application instances across selected subnets/AZs.
4. **Application Load Balancer:** Receives HTTP/HTTPS traffic and routes requests to a target group.
5. **Health checks:** Help determine which targets are healthy.
6. **Security groups:** Restrict traffic between the public entry point and application instances.
7. **EBS volumes:** Store the root filesystem and any required persistent data.
8. **Snapshots:** Back up eligible volume data according to a policy.
9. **Monitoring and logs:** Help detect issues and troubleshoot performance.
10. **Infrastructure as Code and CI/CD:** Terraform or another IaC tool provisions infrastructure; a pipeline builds, tests, and deploys application releases.

**Typical request flow:** User → Load Balancer → Healthy Application EC2 Instances → Application Dependencies.

The exact design depends on whether the application is public or internal, which data must persist, and the security, availability, and recovery requirements.

### What the DevOps engineer does
- Provisions resources with Terraform or approved tools.
- Builds, tests, or selects approved AMIs.
- Maintains launch templates and Auto Scaling configuration.
- Configures listeners, target groups, and health checks.
- Applies least-privilege network and access controls.
- Automates backups and tests recovery.
- Monitors deployments, capacity, health, and cost.

---

## 17. Interview Questions

### Q1. What is EC2?
EC2 is an AWS service that provides virtual servers in the cloud. We choose an operating system, instance type, storage, and networking settings to run applications and tools.

### Q2. What is an AMI?
An AMI is a template used to launch EC2 instances. It helps launch servers with a consistent operating system and captured software configuration.

### Q3. Can we directly replace the AMI of an existing instance?
Creating a new AMI does not replace the image of a running instance. A common approach is to launch a replacement from the new AMI and migrate or reconnect required data and configuration.

### Q4. What is an EBS volume?
It is persistent block storage that can be attached to a compatible EC2 instance and used for an operating system, applications, or data.

### Q5. Why is a root volume created when an EBS-backed EC2 instance launches?
The AMI's block-device mapping defines the root storage configuration. AWS typically creates the root EBS volume so the instance has a bootable operating-system disk.

### Q6. How do you create and attach an additional EBS volume?
Create the volume in the EC2 Volumes section, select its AZ, attach it to a compatible instance in the same AZ, then prepare and mount or initialize it in the operating system as required.

### Q7. What is an EBS snapshot?
A point-in-time backup of an EBS volume. It can be used to create a new volume and restore captured data.

### Q8. Can an EBS volume be attached to an instance in another AZ?
A standard EBS volume is AZ-specific and must be attached to a compatible instance in the same AZ. To use the data in another AZ, create a snapshot and create a new volume from it in the target AZ, subject to supported options.

### Q9. What is the difference between AMI and snapshot?
An AMI is a template for launching an EC2 instance. A snapshot is a point-in-time backup of an EBS volume. An EBS-backed AMI uses snapshots of its backing EBS volumes.

### Q10. Why use a separate data volume?
For suitable workloads, it can separate data from the OS and make storage growth, backup, recovery, and instance replacement easier to manage.

### Q11. What is a load balancer?
It distributes incoming traffic across configured targets. With suitable health checks and architecture, it can avoid sending traffic to targets marked unhealthy.

### Q12. What is the difference between ALB and NLB?
ALB is designed for application-layer HTTP/HTTPS routing and supports rules such as hostname and path routing. NLB handles supported transport-layer traffic such as TCP, UDP, and TLS and is suited to high-performance connections.

### Q13. What is a target group?
A set of targets to which a load balancer routes traffic, with associated health-check settings.

### Q14. What is an Auto Scaling Group?
It manages a group of EC2 instances and helps maintain desired capacity, scaling according to configured policies and replacing instances considered unhealthy.

### Q15. How do AMI, launch template, ASG, and load balancer work together?
The launch template specifies settings such as AMI and instance type. The ASG uses the template to manage instances. The load balancer routes requests to registered healthy targets.

### Q16. Is a snapshot enough to recover a whole application?
Not always. A snapshot restores captured volume data, but recovery may also require instance settings, networking, permissions, application configuration, dependencies, and application-consistent backup procedures.

### Q17. Why test backups?
A backup is useful only if it can be restored within required recovery time and acceptable data loss. Testing reveals missing steps, permission issues, or incomplete backups.

---

## 18. Quick Revision

| Topic | Remember this |
|---|---|
| EC2 | Virtual server in AWS |
| AMI | Template used to launch instances |
| Instance type | Defines compute capacity and characteristics |
| Launch template | Reusable EC2 launch configuration |
| EBS | Persistent block storage for EC2 |
| Root volume | Boot disk for an EBS-backed instance |
| Additional volume | Separate disk attached to an instance |
| Snapshot | Point-in-time backup of an EBS volume |
| Security group | Controls allowed inbound and outbound traffic |
| Key pair | Public/private key pair for supported authentication |
| Elastic IP | Static public IPv4 address for supported resources |
| Network interface | Virtual network card in a VPC |
| Load balancer | Distributes traffic across targets |
| ALB | HTTP/HTTPS application-layer routing |
| NLB | Transport-layer connection handling |
| GWLB | Directs supported traffic through network appliances |
| CLB | Older-generation load balancer for legacy systems |
| Target group | Traffic destinations plus health-check settings |
| Auto Scaling Group | Maintains and adjusts EC2 instance capacity |
| Spot Instances | Discounted spare capacity that can be interrupted |
| Savings Plans / Reserved Instances | Pricing options for eligible committed usage |
| Capacity Reservation | Reserves supported EC2 capacity in a particular AZ |
| Data Lifecycle Manager | Automates supported snapshot/AMI lifecycle policies |

## Final takeaway

EC2 is more than a virtual machine. It is part of an ecosystem for compute, images, storage, networking, security, traffic distribution, scaling, and recovery.

For DevOps, learn not only where to click in the AWS Console, but also **why each resource exists, how resources depend on each other, how to automate them, and how to recover safely when something fails**.
