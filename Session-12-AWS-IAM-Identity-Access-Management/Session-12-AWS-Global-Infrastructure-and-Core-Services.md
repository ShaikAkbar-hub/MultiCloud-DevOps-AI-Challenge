# 🔥 Session 12 — AWS Global Infrastructure & Core Services

**Program:** 55-Session Multi Cloud + DevOps with AI  
**Session:** 12/55  
**Module:** AWS Cloud Fundamentals  
**Practical:** Launching an EC2 Instance and Installing Nginx Using MobaXterm

---

## 📚 Table of Contents

1. [AWS Global Infrastructure](#1-what-is-aws-global-infrastructure)
2. [AWS Regions](#2-what-is-an-aws-region)
3. [Availability Zones](#3-what-is-an-availability-zone-az)
4. [Edge Locations and CloudFront POPs](#4-what-are-edge-locations-and-cloudfront-pops)
5. [Core AWS Services](#5-what-are-the-core-services-of-aws)
6. [Amazon SQS](#6-what-is-amazon-sqs)
7. [Amazon SNS](#7-what-is-amazon-sns)
8. [How SQS and SNS Work Together](#8-how-do-sqs-and-sns-work-together)
9. [Amazon CloudWatch](#9-what-is-amazon-cloudwatch)
10. [EBS, EFS and S3](#10-aws-storage-services--ebs-efs-and-s3)
11. [Elastic Load Balancing](#11-what-is-elastic-load-balancing-elb)
12. [Other Important AWS Services](#12-other-important-aws-services)
13. [EC2 and Nginx Practical](#13-practical--launch-ec2-and-install-nginx-using-mobaxterm)
14. [Key Takeaways](#14-session-12--key-takeaways)

---

## 1. What Is AWS Global Infrastructure?

AWS Global Infrastructure is the worldwide network of data centers and networking facilities that AWS uses to provide cloud services.

It helps organizations deploy applications closer to users, improve availability, reduce latency, and build disaster recovery solutions.

The main components are:

- **Regions:** Separate geographical areas where AWS operates.
- **Availability Zones (AZs):** Isolated groups of data centers within a Region.
- **Edge Locations:** Locations used to deliver content closer to end users through services such as Amazon CloudFront.

### Relationship Between Region, AZ and Edge Location

```text
AWS Global Infrastructure
│
├── Region: Mumbai (ap-south-1)
│   │
│   ├── Availability Zone A
│   │   └── Data Centers
│   │
│   ├── Availability Zone B
│   │   └── Data Centers
│   │
│   └── Availability Zone C
│       └── Data Centers
│
└── Edge Locations
    └── Cache and deliver content closer to users
```

*Note: This is a simplified conceptual diagram, not a complete map of AWS infrastructure.*

## 2. What Is an AWS Region?

An AWS Region is a separate geographical area in which AWS operates cloud infrastructure.

Examples:

- Mumbai — `ap-south-1`
- Hyderabad — `ap-south-2`
- Singapore — `ap-southeast-1`
- Northern Virginia — `us-east-1`

### Why Do We Use Regions?

- To deploy applications close to users.
- To reduce network latency.
- To meet geographical data residency requirements.
- To support disaster recovery across geographical areas.
- To choose services and pricing that suit business requirements.

**Example:** An Indian company may deploy its application in the Mumbai Region to serve Indian users with relatively low network latency.

## 3. What Is an Availability Zone (AZ)?

An Availability Zone is an isolated location within an AWS Region, consisting of one or more data centers with independent infrastructure.

Each Region has multiple Availability Zones.

### Why Do We Use Availability Zones?

- To improve application availability.
- To reduce the impact of a data center failure.
- To deploy redundant application servers.
- To support fault-tolerant architectures.

**Example:** If an application runs on EC2 instances in two different AZs, it may continue operating if one AZ experiences a failure, provided traffic routing and the remaining resources are configured correctly.

**Interview point:** A Region is a geographical area; an AZ is an isolated infrastructure location within that Region.

## 4. What Are Edge Locations and CloudFront POPs?

An **Edge Location** is an AWS infrastructure location used to deliver content closer to users.

A **Point of Presence (POP)** is a networking location through which AWS can provide services such as content delivery. CloudFront POPs commonly include edge locations.

### What Is Amazon CloudFront?

Amazon CloudFront is a Content Delivery Network (CDN) that delivers content to users through a distributed network of edge locations.

### Why Do We Use CloudFront?

- To reduce content delivery latency.
- To cache frequently requested content.
- To reduce repeated requests to the origin server.
- To improve website performance.
- To support secure content delivery using HTTPS and other security features.

**Example:** When a user opens a website containing images, CSS files and JavaScript, CloudFront can serve cached copies from a nearby edge location instead of fetching every file from the origin each time.

**Remember:** A Region is not the same as an edge location. Edge locations are primarily used for delivering content closer to users.

## 5. What Are the Core Services of AWS?

AWS provides services for computing, storage, networking, databases, security, monitoring, automation and application integration.

| AWS Service | Purpose |
|---|---|
| Amazon EC2 | Virtual servers in the cloud |
| Amazon S3 | Object storage for files and data |
| Amazon VPC | Logically isolated networking |
| Amazon EBS | Block storage for EC2 workloads |
| Amazon EFS | Shared, elastic file storage |
| Amazon RDS | Managed relational databases |
| Amazon DynamoDB | Managed NoSQL database |
| AWS IAM | Users, roles, permissions and access control |
| AWS Lambda | Runs code without managing servers |
| Amazon CloudFront | Content delivery network |
| Amazon CloudWatch | Metrics, logs, monitoring and alarms |
| Amazon SQS | Message queuing |
| Amazon SNS | Pub/sub messaging and notifications |
| Amazon API Gateway | Creates and manages APIs |
| AWS CloudTrail | Records AWS account and API activity |
| AWS CloudFormation | Infrastructure as Code using templates |
| Amazon EKS | Managed Kubernetes control plane |
| AWS Step Functions | Coordinates workflows across services |

## 6. What Is Amazon SQS?

**Amazon Simple Queue Service (SQS)** is a fully managed message queuing service that allows applications and services to communicate asynchronously.

It helps separate the component that produces a message from the component that processes it.

### How Does SQS Work?

1. A producer application sends a message to an SQS queue.
2. SQS stores the message until it is processed or expires.
3. A consumer application retrieves the message.
4. The consumer processes the message.
5. After successful processing, the consumer deletes the message.

### Example

An e-commerce application receives 1,000 orders during a busy period. Instead of processing every order immediately in the web application, it places order messages into an SQS queue. Worker applications process the orders independently.

### Why Do We Use SQS?

- To decouple application components.
- To handle traffic spikes.
- To buffer messages for later processing.
- To improve resilience when consumers are temporarily unavailable.
- To enable asynchronous processing.

**Important:** SQS does not automatically process business logic. A consumer must retrieve and process the messages.

## 7. What Is Amazon SNS?

**Amazon Simple Notification Service (SNS)** is a fully managed messaging service that follows the publish/subscribe (pub/sub) model.

A publisher sends a message to an SNS topic, and SNS distributes that message to subscribed endpoints.

### How Does SNS Work?

1. Create an SNS topic.
2. Subscribe endpoints such as SQS queues, Lambda functions, email addresses or HTTP/S endpoints, as supported.
3. A publisher sends a message to the topic.
4. SNS delivers the message to eligible subscribers.

### Example

When an online order is placed, an application publishes an order event to an SNS topic. Subscribers can receive the event for different purposes, such as order processing, analytics or notifications.

### Why Do We Use SNS?

- To send notifications.
- To distribute events to multiple subscribers.
- To connect loosely coupled applications.
- To trigger downstream processing.

**Remember:** SNS is primarily for publishing and distributing messages; SQS is primarily for queuing messages until consumers process them.

## 8. How Do SQS and SNS Work Together?

SNS and SQS can be integrated to distribute messages to multiple independent queues.

### Architecture

```text
                Application
                     |
                     v
                 SNS Topic
                /    |    \
               v     v     v
           SQS Queue A  SQS Queue B  SQS Queue C
               |          |          |
               v          v          v
           Worker A    Worker B    Worker C
```

### Example: E-Commerce Application

When a customer places an order:

1. The application publishes an `OrderCreated` event to an SNS topic.
2. SNS delivers the event to subscribed SQS queues.
3. Queue A is consumed by the order-processing service.
4. Queue B is consumed by the inventory service.
5. Queue C is consumed by the notification service.
6. Each worker processes its own messages independently.

### Benefits of SNS + SQS

- Independent processing by different services.
- Better decoupling between application components.
- Message buffering when a consumer is unavailable.
- Improved resilience and scalability.
- Independent retry and dead-letter queue strategies.

**Interview answer:** SNS distributes messages to subscribers, while SQS stores messages in queues until consumers process them. Together, they support event-driven and loosely coupled architectures.

## 9. What Is Amazon CloudWatch?

Amazon CloudWatch is a monitoring and observability service used to collect and analyze metrics, logs and events from AWS resources and applications.

### Why Do We Use CloudWatch?

- To monitor EC2 CPU utilization.
- To collect application and system logs.
- To create alarms when thresholds are exceeded.
- To create dashboards.
- To investigate application performance problems.
- To trigger automated actions through supported integrations.

### Example

Suppose an EC2 instance's CPU utilization remains above 80%.

1. CloudWatch receives CPU utilization metrics.
2. A CloudWatch alarm evaluates the metric against its configured threshold.
3. The alarm changes state if the condition is met.
4. An SNS notification can alert the operations team.

### Important CloudWatch Components

- **Metrics:** Numerical measurements, such as CPU utilization.
- **Logs:** Records generated by applications and systems.
- **Alarms:** Monitor metric conditions and change state.
- **Dashboards:** Visualize metrics and other monitoring information.

**Interview point:** CloudWatch is used for monitoring and observability. AWS CloudTrail, by contrast, records AWS account activity and API calls.

## 10. AWS Storage Services — EBS, EFS and S3

AWS provides different storage services depending on performance, access patterns and the type of data being stored.

### 10.1 Amazon EBS — Elastic Block Store

Amazon EBS provides persistent block storage volumes primarily for EC2 instances.

**Use cases:**

- Operating system disks.
- Application data requiring block storage.
- Databases running on EC2.
- Workloads that need specific IOPS and throughput performance.

**Example:** An EC2 instance running Linux can use an EBS volume as its root disk, where the operating system and installed software reside.

**Remember:** EBS is block storage, not an object or shared file storage service. EBS volumes are generally associated with a specific Availability Zone.

### 10.2 Amazon EFS — Elastic File System

Amazon EFS is a managed, elastic file storage service that supports the NFS protocol and can be accessed concurrently by multiple supported compute resources.

**Use cases:**

- Shared application files.
- Shared web content across multiple EC2 instances.
- Applications that need a common file system.

**Example:** Multiple EC2 instances running a web application can access the same EFS file system.

**Remember:** EFS is shared file storage; it is not simply a replacement for every EC2 operating system disk.

### 10.3 Amazon S3 — Simple Storage Service

Amazon S3 is an object storage service designed to store and retrieve data at scale.

**Use cases:**

- Images, videos and documents.
- Backups and archives.
- Application logs and data lakes.
- Static website assets.
- Long-term data retention.

Data is stored as objects inside S3 buckets.

**Example:** A company stores customer-uploaded images and backup files in an S3 bucket.

**Remember:** S3 is object storage. It is not the same as an EC2-attached block disk or a shared file system.

### Storage Comparison

| Feature | EBS | EFS | S3 |
|---|---|---|---|
| Storage type | Block | File | Object |
| Typical use | OS disks, databases | Shared application files | Backups, media, archives |
| Access pattern | Block device attached to compute | Shared file system | API-based object access |
| Shared access | Usually associated with one AZ and attached to supported instances | Multiple clients can access it | Multiple authorized clients can access objects |
| Capacity model | Provisioned volume size | Elastic file storage | Elastic object storage |

**Easy way to remember:**

- **EBS = Disk**
- **EFS = Shared files**
- **S3 = Objects and buckets**

Choose a service based on workload requirements, not only on whether the data needs to be stored for a short or long period.

## 11. What Is Elastic Load Balancing (ELB)?

Elastic Load Balancing automatically distributes incoming traffic across multiple registered targets, such as EC2 instances, containers or IP addresses.

### Why Do We Use ELB?

- To distribute traffic across multiple targets.
- To improve application availability.
- To perform health checks on targets.
- To avoid sending traffic to unhealthy targets.
- To support scalable application architectures.

### Example Architecture

```text
                Users
                  |
                  v
        Elastic Load Balancer
              /       \
             v         v
          EC2-A      EC2-B
          Nginx      Nginx
             \         /
              v       v
             Web Application
```

### Types of Elastic Load Balancers

| Type | Main purpose |
|---|---|
| Application Load Balancer (ALB) | HTTP/HTTPS traffic; Layer 7 routing |
| Network Load Balancer (NLB) | High-performance Layer 4 traffic handling |
| Gateway Load Balancer (GWLB) | Deploy and scale virtual network appliances |
| Classic Load Balancer (CLB) | Earlier-generation load balancer |

### Example

If 1,000 users access a website, an ALB can route requests to multiple healthy EC2 instances rather than relying on a single server.

**Interview point:** A load balancer distributes traffic, while Auto Scaling can adjust the number of EC2 instances based on demand. They can be used together.

## 12. Other Important AWS Services

### Amazon EC2 — Elastic Compute Cloud

Provides virtual servers on which we can install operating systems, web servers and applications.

**Example:** Launch an Ubuntu EC2 instance and install Nginx.

### Amazon VPC — Virtual Private Cloud

Provides a logically isolated network in AWS where we configure subnets, route tables and network security.

**Example:** Deploy web servers in public subnets and databases in private subnets.

### Amazon RDS — Relational Database Service

A managed relational database service supporting database engines such as MySQL and PostgreSQL.

**Example:** Store application user and transaction records in a relational database.

### Amazon DynamoDB

A managed NoSQL database designed for low-latency access at scale.

**Example:** Store application session information or product catalog data.

### AWS IAM — Identity and Access Management

Controls who can access AWS resources and which actions they can perform.

**Example:** Assign an IAM role to an EC2 instance so it can access an S3 bucket without storing long-term credentials on the server.

### AWS Lambda

Runs code in response to events without requiring you to manage the underlying servers.

**Example:** Automatically resize an image when it is uploaded to an S3 bucket.

### Amazon API Gateway

Creates, publishes, secures and manages APIs that applications use to communicate with backend services.

**Example:** Expose an API that invokes a Lambda function.

### AWS CloudTrail

Records AWS account activity and API events, helping teams investigate who performed an action and when.

**Example:** Investigate which identity modified a security group.

### AWS CloudFormation

Uses templates to create, update and manage AWS infrastructure as code.

**Example:** Define a VPC, subnets and EC2 resources in a template rather than creating each manually.

### Amazon EKS — Elastic Kubernetes Service

A managed Kubernetes service for deploying and managing containerized applications.

**Example:** Run containerized microservices on Kubernetes in AWS.

### AWS Step Functions

Coordinates multiple steps in an application workflow using state machines.

**Example:** Orchestrate an order workflow involving payment, inventory checks and notifications.

## 13. Practical — Launch EC2 and Install Nginx Using MobaXterm

### Objective

Launch an EC2 instance, connect to it using MobaXterm over SSH, and install the Nginx web server.

### Step 1: Launch an EC2 Instance

1. Open the AWS Management Console.
2. Navigate to **EC2 → Instances → Launch instances**.
3. Enter an instance name.
4. Select an Amazon Machine Image (AMI), such as Ubuntu Server.
5. Select an appropriate instance type.
6. Create or select a key pair.
7. Configure the network and security group.
8. Launch the instance.

### Step 2: Configure the Security Group

Allow the required inbound traffic:

| Type | Port | Purpose |
|---|---:|---|
| SSH | 22 | Connect to the server |
| HTTP | 80 | Access the Nginx website |

For SSH, restrict the source to your own public IP address whenever possible. For a public test website, allow HTTP from the intended audience.

### Step 3: Connect Using MobaXterm

1. Open MobaXterm.
2. Select **Session → SSH**.
3. Enter the EC2 instance's public IPv4 address or public DNS name.
4. Enter the appropriate SSH username:
   - Ubuntu: `ubuntu`
   - Amazon Linux: `ec2-user`
5. Configure the private key in the SSH advanced settings.
6. Connect to the instance.

The username depends on the AMI selected.

### Step 4: Update the Package Index

For Ubuntu:

```bash
sudo apt update
```

### Step 5: Install Nginx

```bash
sudo apt install nginx -y
```

### Step 6: Start Nginx

```bash
sudo systemctl start nginx
```

Enable Nginx to start automatically after reboot:

```bash
sudo systemctl enable nginx
```

### Step 7: Verify Nginx Status

```bash
sudo systemctl status nginx
```

Check the local HTTP response:

```bash
curl http://localhost
```

If Nginx is running successfully, you should receive an HTTP response containing the default web page HTML.

### Step 8: Access the Website

Open a browser and enter:

```text
http://YOUR_EC2_PUBLIC_IP
```

Replace `YOUR_EC2_PUBLIC_IP` with your instance's actual public IPv4 address.

If everything is configured correctly, the Nginx welcome page should appear.

### Troubleshooting

If the website does not load, check:

- The EC2 instance is running.
- Nginx is active.
- Inbound TCP port 80 is allowed in the security group.
- The instance has a reachable public IP and appropriate routing.
- The subnet's route table and internet gateway configuration are correct, where required.
- The operating system firewall is not blocking HTTP traffic.

**Security note:** Do not expose SSH to the entire internet unless there is a justified need and suitable controls. Stop or terminate test resources when no longer needed to avoid unnecessary charges.

## 14. Session 12 — Key Takeaways

By the end of Session 12, I learned:

- AWS Global Infrastructure consists of Regions, Availability Zones and edge infrastructure.
- Regions help with geographical deployment, while multiple AZs support high availability.
- CloudFront uses edge locations to deliver cached content closer to users.
- SQS queues messages for asynchronous processing.
- SNS distributes messages to subscribers.
- SNS and SQS can work together to build event-driven applications.
- CloudWatch monitors AWS resources and applications.
- EBS provides block storage, EFS provides shared file storage, and S3 provides object storage.
- Elastic Load Balancing distributes traffic across healthy targets.
- Core AWS services support compute, networking, databases, security, monitoring and automation.
- In the practical session, I launched an EC2 instance, connected through MobaXterm and installed the Nginx web server.

**Today's practical outcome:** Hands-on experience connecting to a cloud-based Linux server and setting up a web server.

---

#AWS #AmazonWebServices #CloudComputing #DevOps #EC2 #S3 #SQS #SNS #CloudWatch #CloudFront #Linux #Nginx #LearningJourney
