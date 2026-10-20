# Session 13/55 — AWS EC2, AMIs, EBS, Load Balancing & Auto Scaling

**Program:** 55-Session Multi Cloud + DevOps with AI  
**Day:** 4 of AWS  
**Session:** 13 of 55

## 1. What is an AMI?

**AMI (Amazon Machine Image)** is a template used to launch EC2 instances. It contains information needed to launch an instance, such as the operating system and the configuration for its storage.

Examples include Amazon Linux and Ubuntu AMIs. You can also create a **custom AMI** from an existing EC2 instance after installing software and configuring it.

### Why do we use an AMI?

- To launch new EC2 instances with a consistent setup.
- To reuse a prepared operating system and software environment.
- To help replace or recover instances.
- To avoid manually installing the same software on every new instance.

## 2. How to create a custom AMI

1. Launch an EC2 instance using an existing AMI.
2. Connect to the instance and install the required software, such as Docker or Jenkins.
3. Configure the operating system and applications.
4. In the AWS Console, open **EC2 → Instances** and select the instance.
5. Choose **Actions → Image and templates → Create image**.
6. Enter an image name and create the image.
7. Wait until the new AMI is available under **EC2 → AMIs → Owned by me**.
8. Select the custom AMI and choose **Launch instance from AMI** to create another EC2 instance.

The new instance starts with the software and configuration captured in the AMI. You should not need to reinstall software that was successfully captured, although you may still need to configure instance-specific settings, secrets, networking, or application data.

**Important:** Creating an AMI does not replace the AMI of an existing EC2 instance. To apply a new image to a server, launch a replacement instance from the new AMI and migrate workloads/data as needed, or update the existing server directly.

## 3. What is the EC2 Dashboard?

The **EC2 Dashboard** is the central console page for viewing and managing EC2 resources. It provides an overview and shortcuts to instances, images, storage, networking, load balancing, and scaling features.

## 4. What is AWS Global View in EC2?

**AWS Global View** helps you view selected EC2 resources across multiple AWS Regions from one place. It is useful when managing resources in more than one Region, rather than checking each Region separately.

## 5. What are EC2 events?

EC2 events are notifications or scheduled events related to EC2 resources. They can help you identify actions or changes that may affect instances, such as AWS maintenance events. Review the event details and recommended actions in the console.

## 6. EC2 console sections and their purpose

The EC2 console groups related features together. The exact menu items shown may vary by Region, account permissions, and AWS console updates.

### A. Instances

- **Instances:** View, launch, connect to, stop, start, reboot, and terminate virtual servers.
- **Instance types:** Choose the combination of CPU, memory, networking, and other resources for an instance.
- **Launch templates:** Save reusable launch settings, such as the AMI, instance type, networking, and storage.
- **Spot requests:** Request spare EC2 capacity at discounted prices; Spot capacity can be interrupted by AWS.
- **Savings Plans:** Discount programs that can reduce eligible compute costs when you commit to a consistent amount of usage.
- **Reserved Instances:** A pricing option that can provide discounts for eligible usage in exchange for a commitment.
- **Dedicated Hosts:** Physical servers dedicated to your use, often for licensing or compliance needs.
- **Capacity Reservations:** Reserve EC2 capacity for a particular instance type in a specific Availability Zone.
- **Capacity management:** Tools and views for managing or planning compute capacity. Availability of specific entries may vary.
- **UltraServers:** Specialized high-performance EC2 infrastructure offerings, where available.

### B. Images

- **AMIs:** View and manage Amazon Machine Images that can be used to launch instances.
- **AMI Catalog:** Discover available images, including AWS-provided, Marketplace, and other shared images, depending on the console view.

### C. Elastic Block Store (EBS)

- **Volumes:** Persistent block-storage devices that can be attached to EC2 instances.
- **Snapshots:** Point-in-time backups of EBS volumes, stored and managed by AWS.
- **Lifecycle Manager:** Automates the creation, retention, and deletion of EBS snapshots and supported AMIs according to policies.

### D. Network and Security

- **Security Groups:** Virtual firewalls that control inbound and outbound traffic for associated resources.
- **Elastic IP addresses:** Static public IPv4 addresses that can be allocated to your account and associated with supported resources.
- **Placement Groups:** Ways to influence how instances are placed on underlying hardware to meet performance or availability goals.
- **Key Pairs:** Public/private key pairs used for supported secure instance connections, such as SSH to Linux instances.
- **Network Interfaces:** Virtual network cards that connect instances to a VPC and carry network settings such as private IP addresses and security groups.

### E. Load Balancing

- **Load Balancers:** Distribute incoming traffic across registered targets, such as EC2 instances.
- **Target Groups:** Define the targets that receive traffic and the health checks used to assess them.
- **Trust Stores:** Store certificate-authority certificates for supported load balancer mutual TLS configurations.

### F. Auto Scaling

- **Auto Scaling Groups (ASGs):** Maintain a desired number of EC2 instances and can scale capacity up or down according to policies and health checks.

## 7. Types of AWS Load Balancers

AWS Elastic Load Balancing has four commonly discussed load balancer types:

| Type | OSI layer | What it does | Common use |
|---|---|---|---|
| **Application Load Balancer (ALB)** | Layer 7 — Application | Routes HTTP/HTTPS traffic using rules such as hostnames, paths, and headers | Websites, web applications, APIs, microservices |
| **Network Load Balancer (NLB)** | Layer 4 — Transport | Handles TCP, UDP, and TLS traffic with high performance and low latency | High-throughput applications, TCP/UDP services, static IP requirements |
| **Gateway Load Balancer (GWLB)** | Layer 3/4 forwarding for network appliances; commonly described as operating at Layer 3 for traffic steering | Sends traffic through virtual network appliances, such as firewalls and inspection systems | Centralized security inspection and traffic filtering |
| **Classic Load Balancer (CLB)** | Layer 4 and Layer 7 | Earlier-generation load balancer for basic TCP and HTTP/HTTPS load balancing | Legacy applications; for new designs, ALB or NLB is generally preferred |

**Simple way to remember:**
- **ALB:** Understands HTTP/HTTPS requests and can route based on application-level rules.
- **NLB:** Routes network connections and transport-layer traffic.
- **GWLB:** Helps insert virtual network appliances into traffic paths.
- **CLB:** Older-generation option for existing/legacy deployments.

Choose a load balancer based on protocol, routing requirements, performance, and application architecture.

## 8. EC2, EBS volumes, snapshots, and recovery

### What is an EBS volume?

An **EBS volume** is persistent block storage for an EC2 instance. An EBS-backed EC2 launch typically creates a root volume based on the AMI's block-device mapping. Additional volumes can be created and attached separately.

### What is an EBS snapshot?

An **EBS snapshot** is a point-in-time backup of an EBS volume. It can be used to create another volume and restore data.

### Backup and recovery workflow

1. Launch EC2-A. Its AMI specifies the root storage configuration; AWS typically creates the root EBS volume for an EBS-backed instance.
2. Store the required operating system, application, or data on the volume.
3. Create an EBS snapshot of the volume.
4. If EC2-A is lost, create a new EBS volume from the snapshot.
5. Attach the new volume to a compatible EC2 instance in the same Availability Zone as that volume.
6. Mount the volume or otherwise access the restored data as appropriate.

**Important considerations:**
- Creating an AMI and creating a snapshot are separate actions, though creating an EBS-backed AMI uses snapshots of its backing EBS volumes.
- A snapshot only includes data captured at the time of the backup. For application-consistent backups, prepare the application and filesystem as needed.
- EBS volumes are Availability Zone-specific. A snapshot can be used to create a volume in a selected compatible Availability Zone.
- A snapshot of a data volume can restore that data volume; it does not automatically recreate every part of the original EC2 setup, such as its security groups, IP addresses, or instance settings.
- Keep important backups in line with your retention and recovery requirements.

## 9. AMI vs EBS snapshot vs EBS volume

| Resource | Simple definition | Main purpose |
|---|---|---|
| **AMI** | A template used to launch EC2 instances | Recreate servers with a captured OS and configuration |
| **EBS snapshot** | A point-in-time backup of an EBS volume | Back up and restore volume data |
| **EBS volume** | Persistent block storage | Store an operating system, applications, or data for an instance |

**Remember:** An AMI can be used to launch an instance. A snapshot can be used to create a new EBS volume. A volume can then be attached to a compatible EC2 instance.

## 10. Key takeaway

**EC2 is more than a virtual machine.** It is part of a wider ecosystem for compute, images, storage, networking, security, load balancing, and Auto Scaling.

- **Compute:** EC2 instances and instance types
- **Images:** AMIs and the AMI catalog
- **Storage:** EBS volumes and snapshots
- **Networking and security:** VPC networking, security groups, key pairs, and Elastic IPs
- **Traffic distribution:** Load balancers and target groups
- **Scaling:** Auto Scaling Groups

These services work together to help deploy, operate, scale, and recover applications in AWS.
