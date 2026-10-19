# Session 11 — AWS Elasticity, Scaling, Auto Scaling, High Availability, AMI, CloudFront & EC2

## Table of Contents

- [1. Elasticity](#1-elasticity)
- [2. Scaling](#2-scaling)
  - [Horizontal Scaling](#horizontal-scaling)
  - [Vertical Scaling](#vertical-scaling)
- [3. AWS Auto Scaling](#3-aws-auto-scaling)
- [4. High Availability (HA)](#4-high-availability-ha)
- [5. Disaster Recovery (DR)](#5-disaster-recovery-dr)
- [6. AMI (Amazon Machine Image)](#6-ami-amazon-machine-image)
- [7. AWS Regions, Availability Zones, and Latency](#7-aws-regions-availability-zones-and-latency)
- [8. Amazon CloudFront and CDN](#8-amazon-cloudfront-and-cdn)
- [9. What Happens When an EC2 Instance Is Terminated?](#9-what-happens-when-an-ec2-instance-is-terminated)
- [10. AWS Marketplace and Trusted AMIs](#10-aws-marketplace-and-trusted-amis)
- [11. EC2 Instance Types](#11-ec2-instance-types)
- [12. Practical: Launching an EC2 Instance](#12-practical-launching-an-ec2-instance)
- [13. Quick Revision](#13-quick-revision)

---

## 1. Elasticity

**Elasticity** is the ability of a cloud environment to automatically or manually increase or decrease resources based on changing demand.

For example:
- During a sale, an e-commerce website may need more servers to handle increased traffic.
- At night, when traffic decreases, it may reduce the number of servers to save costs.

**Key idea:** Elasticity helps match resources to demand, improving performance and controlling costs.

> Elasticity is related to changing capacity as demand changes. It is not limited to adding or removing EC2 instances.

## 2. Scaling

**Scaling** means increasing or decreasing computing capacity to meet workload requirements. The two main types are horizontal scaling and vertical scaling.

### Horizontal Scaling

Horizontal scaling means **adding or removing resource instances**.

- **Scale out:** Add more instances or replicas.
- **Scale in:** Remove instances when they are no longer needed.

**Example:** Increase an application fleet from 2 EC2 instances to 5 EC2 instances to handle more traffic.

### Vertical Scaling

Vertical scaling means **increasing or decreasing the capacity of an existing resource**, such as its CPU and memory.

- **Scale up:** Move to a larger instance with more CPU and memory.
- **Scale down:** Move to a smaller instance with less CPU and memory.

**Example:** Change an EC2 instance from a smaller instance type to a larger one.

| Feature | Horizontal Scaling | Vertical Scaling |
|---|---|---|
| How it works | Adds or removes instances | Changes the size/capacity of an instance |
| Example | 2 EC2 instances → 5 EC2 instances | Smaller EC2 instance → larger EC2 instance |
| Common benefit | Can distribute load and improve resilience | Can provide more capacity to one resource |
| Consideration | Application should support multiple instances; a load balancer is often used | May require a restart or downtime, depending on the resource and change |

## 3. AWS Auto Scaling

**AWS Auto Scaling** helps automatically adjust resource capacity to match demand and maintain application availability.

For EC2, an **EC2 Auto Scaling group** can add or remove instances based on scaling policies, schedules, or health checks.

### Does AWS Auto Scaling increase the number of resources as required?

**Yes.** For an EC2 Auto Scaling group, scale-out policies can launch more EC2 instances when demand or a configured metric reaches a threshold. Scale-in policies can remove instances when demand falls, while respecting the group's configured minimum and maximum capacity.

**Example:**
1. An application normally runs on 2 EC2 instances.
2. Traffic increases and a configured scaling policy is triggered.
3. Auto Scaling launches additional instances, up to the configured maximum.
4. When demand decreases, it can remove excess instances, down to the configured minimum.

AWS Auto Scaling capabilities also apply to other supported services and resources, including Amazon DynamoDB, Amazon Aurora, and Amazon ECS, although the scaling mechanisms differ by service.

**Important:** Auto Scaling does not automatically monitor every application and scale every resource without setup. You configure supported resources, metrics or schedules, and scaling policies.

## 4. High Availability (HA)

**High Availability** means designing an application so that it continues to operate, with minimal interruption, when a component or Availability Zone fails.

Common approaches include:
- Running multiple application instances.
- Distributing traffic with a load balancer.
- Deploying resources across multiple **Availability Zones (AZs)** in a Region.
- Monitoring the application and replacing unhealthy instances.

**Example:** Run application servers in two Availability Zones behind an Application Load Balancer. If one AZ becomes unavailable, traffic can be directed to healthy instances in the other AZ, provided the architecture and capacity support it.

**Correction to remember:** High availability usually uses multiple Availability Zones within the same Region. Deploying across two Regions is a possible multi-Region architecture, but it is not required for every HA design.

In a typical active-active design, multiple instances serve live traffic at the same time.

## 5. Disaster Recovery (DR)

**Disaster Recovery** is the strategy for restoring systems and data after a major disruption, such as a Region-wide outage, accidental deletion, or serious data corruption.

A DR environment may be:
- **Backup and restore:** Keep backups and rebuild when needed.
- **Pilot light:** Keep essential core resources running and scale up during recovery.
- **Warm standby:** Keep a smaller, functioning environment ready to scale up.
- **Multi-site / active-active:** Run workloads in multiple locations and serve traffic from more than one site.

### High Availability vs. Disaster Recovery

| High Availability | Disaster Recovery |
|---|---|
| Focuses on reducing interruptions during component or AZ failures | Focuses on recovery after a significant disruption |
| Often uses multiple AZs in one Region | May use another AZ or, for regional resilience, another Region |
| Multiple instances may serve live traffic | The recovery environment may be idle, partially running, or actively serving traffic, depending on the DR strategy |

**Important:** A standby DR server is not always unused. Warm standby and active-active DR designs keep resources running; the exact design depends on recovery objectives, cost, and business needs.

## 6. AMI (Amazon Machine Image)

An **Amazon Machine Image (AMI)** is a template used to launch an EC2 instance. It contains the information needed to create an instance, including:
- An operating system and its configuration.
- Preinstalled software, if included.
- Launch permissions and block-device mapping information, including references to storage snapshots.

**Is an AMI a bundle of software packages?**

Partly, but that description is incomplete. An AMI is more accurately an **instance-launch template** that includes an operating system and may include applications or other software already installed. It is not simply a collection of software packages.

**Example:** A Windows Server AMI can be used to launch an EC2 instance with Windows Server installed.

### AMI best practices

- Prefer official, verified, or otherwise trusted AMIs from reputable publishers.
- Check the publisher, supported operating system, update status, and security information before using a third-party image.
- Use AWS Marketplace when you need an image or software product published by a third party. Marketplace listings still need to be evaluated; do not assume every listing is automatically safe.

## 7. AWS Regions, Availability Zones, and Latency

- **Region:** A separate geographic area where AWS has infrastructure.
- **Availability Zone (AZ):** An isolated location within a Region, made up of one or more data centers.
- **Data center:** A physical facility that houses computing, storage, and networking equipment.

When choosing where to deploy an application, consider user latency, service availability, compliance and data residency, cost, and the AWS services available in that Region.

For resilience within a Region, applications commonly distribute resources across multiple AZs.

## 8. Amazon CloudFront and CDN

**Amazon CloudFront** is AWS's Content Delivery Network (CDN). It delivers content to users through a globally distributed network of edge locations and regional edge caches.

### What is a CDN?

A **Content Delivery Network (CDN)** caches and delivers eligible content from locations closer to users. This can reduce latency and decrease the number of requests that must reach the origin server.

Examples of cacheable content include images, CSS, JavaScript, downloads, and certain API responses, depending on the cache configuration.

### Do we create a data center inside a CDN?

**No.** You do not create your own application data center inside CloudFront edge locations. You configure CloudFront with an **origin**, such as an S3 bucket, an Application Load Balancer, or an HTTP server, and CloudFront delivers content from its edge network.

Think of it this way:
- **Origin:** The original source of the content.
- **Edge location:** A CloudFront point of presence that can serve cached content near users.
- **Regional edge cache:** An intermediate cache layer that can retain content closer to users than the origin.

### Simplified request flow

1. A user requests content from a CloudFront distribution.
2. CloudFront checks whether the requested content is available in the relevant cache.
3. If it is a cache hit, CloudFront serves the cached content.
4. If it is a cache miss, CloudFront may retrieve the content through its cache hierarchy from the origin, store it according to the cache policy, and return it to the user.

**Important:** Not every request follows a simple, guaranteed sequence of edge location → regional edge cache → origin. The exact path depends on CloudFront's architecture and request handling. Dynamic or non-cacheable content may need to be forwarded to the origin.

## 9. What Happens When an EC2 Instance Is Terminated?

Terminating an EC2 instance is different from stopping it.

- **Stop:** The instance is shut down and can generally be started again. Data on an attached EBS volume is normally retained.
- **Terminate:** The instance is permanently removed and cannot be restarted. EBS volumes are deleted or retained according to their configured `DeleteOnTermination` setting; instance-store data is lost.

**Important correction:** AWS does not guarantee that a terminated instance remains available for 10–15 minutes so you can save its details. Before termination, save any required data, configuration, logs, and metadata. Use AMIs, snapshots, backups, infrastructure-as-code, and tagging as appropriate.

## 10. AWS Marketplace and Trusted AMIs

**AWS Marketplace** is a catalog where customers can find software and machine images offered by AWS and third-party vendors.

Before launching a Marketplace AMI:
- Verify the publisher and product details.
- Review software licensing and charges.
- Check operating system support, security updates, and documentation.
- Follow the organization's approval and security requirements.

Marketplace availability does not remove the need to validate an image before using it.

## 11. EC2 Instance Types

An **EC2 instance type** defines the compute capacity and hardware characteristics available to an instance, including CPU, memory, networking, and sometimes storage capabilities or accelerators.

Choose an instance type based on the workload:
- **General purpose:** Balanced compute, memory, and networking.
- **Compute optimized:** Workloads that benefit from more CPU.
- **Memory optimized:** Workloads that need substantial RAM.
- **Storage optimized:** Workloads with high local storage or storage I/O requirements.
- **Accelerated computing:** Workloads that use GPUs or other accelerators.

**Example:** A memory-intensive application may need a memory-optimized instance, while a CPU-intensive workload may benefit from a compute-optimized instance.

## 12. Practical: Launching an EC2 Instance

### What I practiced

- Launched an EC2 instance using a Windows Server 2025 base AMI.
- Generated a key pair for authentication.
- Used MobaXterm as a third-party client/tool for connecting to the instance.
- Used the generated key pair as part of the login process.

### Key practical reminders

- Select the correct AMI and instance type for the workload.
- Store the private key securely and never share it publicly or commit it to GitHub.
- Configure the security group to allow only the required inbound traffic from trusted sources.
- Use the connection method appropriate to the operating system and configuration. For Windows EC2, RDP is common; SSH is commonly used for Linux. MobaXterm supports multiple connection methods, so the exact method depends on the instance setup.
- Stop or terminate test resources when no longer needed to avoid unnecessary charges. Back up any data you want to keep before termination.

## 13. Quick Revision

- **Elasticity:** Adjust resources as demand changes.
- **Horizontal scaling:** Add or remove instances.
- **Vertical scaling:** Increase or decrease the capacity of an existing instance.
- **Auto Scaling:** Automatically adjusts supported resource capacity using configured policies and signals.
- **High Availability:** Keeps an application running through failures by removing single points of failure.
- **Disaster Recovery:** Restores services after a major disruption.
- **AMI:** A template used to launch EC2 instances with an OS and optional preinstalled software.
- **Region:** A geographic AWS area.
- **Availability Zone:** An isolated location within a Region.
- **CloudFront:** A CDN that can serve cached content from edge locations and regional edge caches.
- **EC2 instance type:** Defines compute capacity and hardware characteristics.
- **AWS Marketplace:** A catalog for AWS and third-party software products and machine images.

---

**Session 11 of 55 — Multi Cloud + DevOps with AI Program**

*Learning focus: AWS elasticity, scaling, Auto Scaling, high availability, disaster recovery, AMIs, CloudFront, and launching an EC2 instance.*
