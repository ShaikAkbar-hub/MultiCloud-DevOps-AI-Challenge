# Day 9 — Session 18 of 55: AWS VPC and Networking

> **Program:** Multi-Cloud DevOps with AI  
> **Topics:** VPC, Regions, Availability Zones, Subnets, Routes, Route Tables, IGW, NAT Gateway, VPC Endpoints, DNS, OSI Model, TCP/UDP, Proxy, and VPC Peering

## Table of Contents

Click a topic to jump directly to its notes.

1. [What Is a VPC?](#1-what-is-a-vpc)
2. [What Is an AWS Region?](#2-what-is-an-aws-region)
3. [What Is an Availability Zone?](#3-what-is-an-availability-zone)
4. [What Is a Subnet?](#4-what-is-a-subnet)
5. [What Is a Route?](#5-what-is-a-route)
6. [What Is a Route Table?](#6-what-is-a-route-table)
7. [What Is an Internet Gateway (IGW)?](#7-what-is-an-internet-gateway-igw)
8. [What Is a NAT Gateway?](#8-what-is-a-nat-gateway)
9. [What Is an Egress-Only Internet Gateway?](#9-what-is-an-egress-only-internet-gateway)
10. [What Are VPC Endpoints?](#10-what-are-vpc-endpoints)
11. [What Is DNS?](#11-what-is-dns)
12. [What Is DNS Resolution?](#12-what-is-dns-resolution)
13. [What Is a DNS Hostname in AWS?](#13-what-is-a-dns-hostname-in-aws)
14. [What Is the OSI Model?](#14-what-is-the-osi-model)
15. [What Is TCP?](#15-what-is-tcp)
16. [What Is UDP?](#16-what-is-udp)
17. [What Is a Proxy Server?](#17-what-is-a-proxy-server)
18. [What Is Peer-to-Peer Communication?](#18-what-is-peer-to-peer-communication)
19. [What Is Unicast?](#19-what-is-unicast)
20. [What Is Broadcast?](#20-what-is-broadcast)
21. [Route Table vs. Linux Firewall](#21-route-table-vs-linux-firewall)
22. [What Is VPC Peering?](#22-what-is-vpc-peering)
23. [Is VPC Peering One-Way or Two-Way?](#23-is-vpc-peering-one-way-or-two-way)
24. [How Do Two VPCs Communicate?](#24-how-do-two-vpcs-communicate)
25. [Example AWS VPC Architecture](#25-example-aws-vpc-architecture)
26. [Important Differences for Interviews](#26-important-differences-for-interviews)
27. [AWS Networking Troubleshooting](#27-aws-networking-troubleshooting)
28. [Quick Revision Definitions](#28-quick-revision-definitions)
29. [Final Revision Questions](#29-final-revision-questions)

---

## 1. What Is a VPC?

### Definition
A **VPC (Virtual Private Cloud)** is a logically isolated virtual network in AWS where we can launch and manage resources such as EC2 instances and databases.

We control its IP address ranges, subnets, routing, and network security settings.

### Why do we use a VPC?
- To create an isolated network for AWS resources.
- To control communication between resources.
- To separate public-facing resources from private resources.
- To manage network traffic using route tables, security groups, and network ACLs.
- To build secure and highly available applications.

### Example
Create a VPC with CIDR `10.0.0.0/16`. Inside it, create:
- A public subnet for an application server.
- A private subnet for a database.
- An Internet Gateway for suitable internet connectivity.
- A NAT Gateway if private IPv4 resources need outbound internet access.

**Remember:** A VPC is the network, not the server.

## 2. What Is an AWS Region?

### Definition
An **AWS Region** is a geographical area where AWS operates its cloud infrastructure. Each Region contains multiple Availability Zones.

### Why do we use Regions?
- To deploy applications closer to users.
- To meet data residency requirements.
- To design disaster recovery solutions.
- To choose where infrastructure is located.

### Example
The Mumbai Region has the code `ap-south-1`.

**Remember:** A VPC belongs to one Region and can span multiple Availability Zones in that Region.

## 3. What Is an Availability Zone?

### Definition
An **Availability Zone (AZ)** is an isolated location within an AWS Region, made up of one or more data centers.

### Why do we use AZs?
- To improve availability.
- To reduce the impact of a failure in one location.
- To distribute resources across separate infrastructure locations.
- To support fault-tolerant designs.

### Example
An application can run EC2 instances in two AZs. If one AZ has a problem, the other may continue serving traffic if the application is designed for failover.

**Remember:** Deploying resources in two AZs helps availability, but does not automatically guarantee uninterrupted service.

## 4. What Is a Subnet?

### Definition
A **subnet** is a range of IP addresses within a VPC. Each subnet belongs to exactly one Availability Zone.

### Why do we use subnets?
- To organize resources.
- To separate public-facing and private resources.
- To apply different routing configurations.
- To design highly available applications.

### Example

VPC CIDR: `10.0.0.0/16`

| Subnet | CIDR | Example purpose |
|---|---|---|
| Public subnet 1 | `10.0.1.0/24` | Application server |
| Public subnet 2 | `10.0.2.0/24` | Application server in another AZ |
| Private subnet 1 | `10.0.11.0/24` | Database |
| Private subnet 2 | `10.0.12.0/24` | Database in another AZ |

### Public vs. private subnet
- **Public subnet:** Its associated route table has a route to an Internet Gateway for internet-bound traffic.
- **Private subnet:** It does not have a direct route to an Internet Gateway for ordinary direct internet access. It may use a NAT Gateway for outbound IPv4 connectivity.

For an EC2 instance to be reachable over public IPv4, it generally also needs a public IPv4 address or Elastic IP and appropriate security rules. An IGW route alone is not enough.

## 5. What Is a Route?

### Definition
A **route** is a rule that tells a network where to send traffic based on the destination IP address.

A route includes a destination and a target.

### Examples
- `10.0.0.0/16 → local`: Routes traffic within the VPC's IPv4 CIDR locally.
- `0.0.0.0/0 → Internet Gateway`: Sends otherwise unmatched IPv4 traffic toward the Internet Gateway.
- `0.0.0.0/0 → NAT Gateway`: Sends otherwise unmatched IPv4 traffic from a private subnet toward a NAT Gateway.

### Why do we use routes?
- To direct traffic to the correct destination.
- To enable internet connectivity.
- To direct private subnet traffic through a NAT Gateway.
- To connect networks through supported connections such as VPC peering.

**Remember:** A route decides where traffic goes. Security rules decide whether traffic is allowed.

## 6. What Is a Route Table?

### Definition
A **route table** is a collection of routing rules that determines where network traffic from an associated subnet should be directed.

Each subnet is associated with a route table. If we do not explicitly associate it with a custom route table, it uses the VPC's main route table.

### Public subnet route table example

| Destination | Target |
|---|---|
| `10.0.0.0/16` | Local |
| `0.0.0.0/0` | Internet Gateway |

### Private subnet route table example

| Destination | Target |
|---|---|
| `10.0.0.0/16` | Local |
| `0.0.0.0/0` | NAT Gateway |

The private subnet's default route sends eligible IPv4 internet-bound traffic through the NAT Gateway.

### Why do we use route tables?
- To direct internet-bound traffic.
- To direct traffic to a NAT Gateway.
- To route traffic between peered VPCs.
- To control network paths.

## 7. What Is an Internet Gateway (IGW)?

### Definition
An **Internet Gateway (IGW)** is an AWS networking component that enables communication between a VPC and the internet when the required routing, IP addressing, and security configurations are in place.

### Why do we use an IGW?
We use it when resources in a VPC need internet connectivity.

### Example: Internet user accessing an EC2 instance
The simplified traffic path is:

`Internet user → Internet Gateway → EC2 instance`

For public IPv4 connectivity, the setup generally needs:
1. An IGW attached to the VPC.
2. A route to the IGW in the subnet's route table.
3. A public IPv4 address or Elastic IP on the EC2 instance.
4. Security group rules allowing the required traffic.
5. Appropriate network ACL rules.
6. A valid return path.

### How to configure an IGW
1. Open the VPC console.
2. Create an Internet Gateway.
3. Attach it to the VPC.
4. Open the route table associated with the public subnet.
5. Add `0.0.0.0/0` with the IGW as the target for IPv4 internet traffic.

For IPv6, an appropriate route such as `::/0` may be used.

### Important points
- An IGW is attached to a **VPC**, not directly to a subnet.
- A subnet is considered public when its routing configuration provides a route to an IGW.
- An IGW does not automatically assign public IP addresses to EC2 instances.
- An IGW is not a firewall; security groups and network ACLs control permitted traffic.

**Interview definition:** An Internet Gateway enables internet communication for a VPC when the necessary routes, public IP addressing, and security rules are configured.

## 8. What Is a NAT Gateway?

### Definition
A **NAT Gateway** allows resources in a private subnet to initiate connections to external destinations without requiring those resources to have public IPv4 addresses. NAT means Network Address Translation.

### Why do we use a NAT Gateway?
A private EC2 instance may need to:
- Download software updates.
- Install packages.
- Access external APIs.
- Download application dependencies.

### Traffic path
`Private EC2 → NAT Gateway → Internet Gateway → Internet`

Return traffic for connections initiated by the private instance travels back through the NAT Gateway.

### Basic setup
1. Create a NAT Gateway in a public subnet.
2. Associate an Elastic IP with a public NAT Gateway.
3. Ensure the public subnet's route table points internet-bound IPv4 traffic to the IGW.
4. Add a route in the private subnet's route table: `0.0.0.0/0 → NAT Gateway`.

### Important points
- A public NAT Gateway is placed in a public subnet.
- Private instances do not need public IPv4 addresses to use it.
- A NAT Gateway does not accept unsolicited inbound connections to private instances.
- Same-VPC communication normally uses the VPC's local route and does not require a NAT Gateway.
- NAT Gateways have usage charges.

**Interview definition:** A NAT Gateway enables private IPv4 resources to initiate outbound connections to external destinations without assigning those resources public IPv4 addresses.

## 9. What Is an Egress-Only Internet Gateway?

### Definition
An **Egress-Only Internet Gateway** allows IPv6 traffic to leave a VPC while preventing the internet from initiating new connections back to IPv6 resources through that gateway.

### Example
`Private IPv6 EC2 → Egress-Only Internet Gateway → Internet`

The EC2 instance can initiate a connection and receive its response traffic, but external systems cannot initiate new connections through the gateway.

### Important points
- It is for IPv6 traffic.
- A NAT Gateway is commonly used for outbound IPv4 connectivity.
- It does not provide IPv4 NAT functionality.
- Appropriate routes and security rules are still required.

**Remember:** NAT Gateway is commonly used for private IPv4 outbound access; Egress-Only Internet Gateway provides outbound-only IPv6 internet access.

## 10. What Are VPC Endpoints?

### Definition
A **VPC endpoint** provides private connectivity from a VPC to supported AWS services or endpoint services without requiring traffic to travel through a public internet path.

### Why do we use VPC endpoints?
- To access supported AWS services privately.
- To reduce dependence on internet connectivity.
- To improve security.
- To reduce NAT Gateway costs in suitable architectures.
- To control access to supported services.

### Example: EC2 accessing S3
A private EC2 instance may use an S3 VPC endpoint to access Amazon S3 without requiring a NAT Gateway for that S3 traffic.

### Types of VPC endpoints

**1. Gateway endpoint**
- Used for Amazon S3 and Amazon DynamoDB.
- Adds routes to the relevant route tables.

**2. Interface endpoint**
- Uses AWS PrivateLink.
- Creates network interfaces with private IP addresses in selected subnets.
- Supports many AWS services and supported endpoint services.

### Important points
- Not every AWS service uses a gateway endpoint.
- Interface endpoints commonly use endpoint security groups.
- Endpoint policies can help control access, alongside IAM and resource policies.
- Creating an endpoint does not automatically grant permission to use the destination service.

**Interview definition:** A VPC endpoint provides private connectivity between a VPC and supported AWS services or endpoint services.

## 11. What Is DNS?

### Definition
**DNS (Domain Name System)** is a naming system that associates domain names with IP addresses and other information.

### Example
A user enters `www.example.com` in a browser. DNS helps the system find the IP address associated with that name.

### Why do we use DNS?
- To use names instead of remembering IP addresses.
- To support service discovery.
- To direct users to application endpoints.
- To manage domain records.

### Common DNS records

| Record | Purpose |
|---|---|
| A | Maps a hostname to an IPv4 address |
| AAAA | Maps a hostname to an IPv6 address |
| CNAME | Points one hostname to another hostname |
| MX | Identifies mail servers for a domain |
| TXT | Stores text information for verification and other purposes |

## 12. What Is DNS Resolution?

### Definition
**DNS resolution** is the process of looking up DNS records for a domain name or hostname.

### Simplified process
1. A browser or operating system checks available DNS caches.
2. If needed, a DNS resolver looks up the required record.
3. The resolver obtains the DNS response.
4. The browser uses the returned IP address to attempt a network connection.

### AWS example
Amazon Route 53 can host DNS records that direct a hostname to an application endpoint. DNS helps find the destination, but routing and security settings determine whether the application is reachable.

### DNS vs. DNS resolution
- **DNS:** The naming system.
- **DNS resolution:** The process of looking up the name.

## 13. What Is a DNS Hostname in AWS?

### Definition
A **DNS hostname** is a human-readable name associated with a network resource or service.

AWS can provide DNS hostnames for resources such as EC2 instances, depending on the resource and configuration.

### Example
An EC2 instance may have a public DNS name resembling:

`ec2-203-0-113-10.compute-1.amazonaws.com`

This is an illustrative hostname, not a real instance address.

### Important points
- A DNS hostname is a name, not an IP address.
- DNS resolution does not guarantee network reachability.
- A public DNS name does not, by itself, make an EC2 instance publicly accessible.
- VPC DNS settings affect hostname assignment and DNS resolution.

## 14. What Is the OSI Model?

### Definition
The **OSI (Open Systems Interconnection) model** is a conceptual model that divides network communication into seven layers.

### Why do we use it?
- To understand network communication.
- To organize networking concepts.
- To troubleshoot connectivity problems.
- To understand protocols and their responsibilities.

### The seven layers

| Layer | Name | Main responsibility | Examples |
|---|---|---|---|
| 7 | Application | Application network services | HTTP, DNS, SMTP |
| 6 | Presentation | Data representation | Encoding, compression |
| 5 | Session | Session management | Logical application sessions |
| 4 | Transport | End-to-end transport | TCP, UDP |
| 3 | Network | IP addressing and routing | IP, routers |
| 2 | Data Link | Local frame delivery | Ethernet, MAC addresses |
| 1 | Physical | Transmission of bits | Cables, radio signals |

### Layer 1: Physical Layer
**Definition:** Transmits raw bits over a physical medium.

**Examples:** Ethernet cables, fiber-optic cables, wireless radio signals.

**Troubleshooting example:** A damaged cable or failed network interface can cause a physical connectivity problem.

**Remember:** Layer 1 deals with bits and signals.

### Layer 2: Data Link Layer
**Definition:** Transfers frames across a local network and uses MAC addresses for local delivery.

**Examples:** Ethernet, MAC addresses, VLANs, switches.

**Troubleshooting example:** An incorrect VLAN configuration may prevent devices from communicating on the expected local network.

**Remember:** Layer 2 deals with frames and local delivery.

### Layer 3: Network Layer
**Definition:** Handles logical addressing and routing packets between networks.

**Examples:** IPv4, IPv6, IP routing, ICMP.

**AWS example:** If an EC2 instance cannot reach a resource in another subnet, check CIDR ranges and route tables, as well as security controls.

**Remember:** Layer 3 deals with IP addresses and routing.

### Layer 4: Transport Layer
**Definition:** Provides transport communication between applications on different systems.

**Examples:** TCP and UDP.

**AWS example:** MySQL commonly uses TCP port `3306`. If the required traffic is blocked, the connection may fail.

**Remember:** Layer 4 deals with transport protocols and port numbers.

### Layer 5: Session Layer
**Definition:** Describes functions for establishing, managing, and terminating communication sessions.

**Example:** An application maintains a logical session while exchanging messages.

In modern systems, session management is often implemented by application protocols and software rather than a separate OSI layer.

**Remember:** Layer 5 concerns communication sessions.

### Layer 6: Presentation Layer
**Definition:** Describes how data is represented so the receiving application can interpret it.

**Functions:** Encoding, serialization, compression, encryption, and decryption.

**Examples:** UTF-8, JSON, XML, JPEG, PNG.

**DevOps example:** A microservice may fail to process a message if the received data format differs from what it expects.

**Remember:** Layer 6 concerns data representation.

### Layer 7: Application Layer
**Definition:** Provides network services used by applications.

**Examples:** HTTP, HTTPS, DNS, SMTP, SSH, FTP.

**DevOps example:** Test an application's HTTP endpoint and inspect the response code when troubleshooting an unavailable service.

**Remember:** Layer 7 is closest to the application using the network.

> **Note:** The OSI model is a conceptual troubleshooting framework. Real-world protocols do not always fit neatly into a single layer.

## 15. What Is TCP?

### Definition
**TCP (Transmission Control Protocol)** is a connection-oriented transport protocol that provides reliable, ordered data delivery between endpoints.

### Why do we use TCP?
- To provide reliable delivery.
- To retransmit lost data when necessary.
- To deliver data in order.
- To manage data flow.

### TCP three-way handshake
1. **SYN:** The client requests a connection.
2. **SYN-ACK:** The server acknowledges the request and responds.
3. **ACK:** The client acknowledges the server's response.

The connection is then established.

### Example
HTTPS over HTTP/1.1 or HTTP/2 commonly uses TCP port `443`. HTTP/3 uses QUIC over UDP.

### DevOps example
For an EC2 instance connecting to Amazon RDS MySQL, check:
- The database endpoint and DNS resolution.
- Network routes.
- The RDS security group's inbound TCP `3306` rule.
- The required outbound rules.
- Database availability and listening status.

**Remember:** TCP provides reliable, ordered delivery, but does not guarantee that the application operation itself will succeed.

## 16. What Is UDP?

### Definition
**UDP (User Datagram Protocol)** is a connectionless transport protocol that sends datagrams without establishing a connection or guaranteeing delivery, ordering, or retransmission.

### Why do we use UDP?
- To keep transport overhead low.
- To support applications where low latency is important.
- To support protocols that implement their own reliability when needed.

### Examples
- DNS queries commonly use UDP.
- Online gaming may use UDP.
- Voice and video applications may use UDP.
- QUIC, used by HTTP/3, runs over UDP.

### TCP vs. UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless |
| Delivery guarantee | Reliable delivery mechanisms | No delivery guarantee |
| Ordering | Ordered delivery | No ordering guarantee |
| Retransmission | Supported | Not provided by UDP itself |
| Overhead | Generally higher | Generally lower |
| Examples | HTTPS over HTTP/2 | DNS queries, QUIC transport |

**Remember:** TCP focuses on reliable, ordered delivery. UDP provides a simpler datagram transport without those built-in guarantees.

## 17. What Is a Proxy Server?

### Definition
A **proxy server** is an intermediary that receives requests from a client and forwards them to another server.

Depending on its type, it can filter requests, cache responses, enforce access policies, or hide the client's address from the destination.

### Why do we use a proxy?
- To control access to external services.
- To filter traffic.
- To cache frequently requested content.
- To centralize network access.
- To provide an intermediary between clients and destination servers.

### Types of proxies

**Forward proxy:** Acts on behalf of clients accessing other servers.

`Client → Forward proxy → External website`

**Reverse proxy:** Acts on behalf of backend servers and receives requests from clients.

`Client → Reverse proxy → Backend application`

### AWS DevOps example
A reverse proxy can receive HTTPS requests and forward them to application servers. AWS load balancers provide related traffic-distribution functions, although a load balancer and a general-purpose proxy are not identical.

**Remember:** A proxy does not automatically secure a backend server; configuration and network controls matter.

## 18. What Is Peer-to-Peer Communication?

### Definition
**Peer-to-peer (P2P)** is a communication model in which systems communicate as peers rather than relying entirely on a central server to relay every interaction.

Peers may communicate directly or use supporting infrastructure.

### Example
Two computers exchange files directly over a network.

### Is peer-to-peer the same as one-to-one?
No.
- **One-to-one** describes a communication pattern: one sender and one receiver.
- **Peer-to-peer** describes a relationship or architecture between participating systems.

A peer-to-peer network can involve many peers.

### AWS example
Two EC2 instances may communicate using private IP addresses if routing and security rules allow it. That communication alone does not necessarily make the application a peer-to-peer architecture.

## 19. What Is Unicast?

### Definition
**Unicast** is a communication pattern in which one sender sends data to one specific receiver.

### Examples
- An EC2 instance connects to a particular database server.
- An administrator connects to an EC2 instance using SSH.
- An application sends a request to a specific server.

**Remember:** Unicast = one sender to one receiver.

## 20. What Is Broadcast?

### Definition
**Broadcast** is a communication pattern in which one sender sends data to all devices within the relevant broadcast domain.

### Example
An IPv4 device may send a broadcast message to discover information about devices on its local network.

### Important points
- IPv4 broadcast is used within a local broadcast domain.
- Routers generally do not forward ordinary IPv4 broadcasts between networks.
- IPv6 does not use broadcast; it uses multicast for corresponding functions.
- AWS VPC networking does not operate exactly like a traditional Ethernet network. Do not assume ordinary broadcast traffic can be sent across VPC subnets.

### Unicast vs. broadcast

| Feature | Unicast | Broadcast |
|---|---|---|
| Communication | One sender to one receiver | One sender to all devices in the broadcast domain |
| Destination | Specific receiver | All applicable devices in the domain |
| Example | Client-server request | Local IPv4 network discovery |

## 21. Route Table vs. Linux Firewall

AWS route tables and Linux firewalls perform different jobs.

### AWS route table
A route table determines where traffic should be forwarded based on its destination.

Example: `0.0.0.0/0 → Internet Gateway`

This directs matching IPv4 traffic toward the IGW.

### Linux `iptables`
`iptables` is a Linux utility for configuring packet-filtering and network-address-translation rules through the kernel's netfilter framework. It can allow, reject, or drop traffic, depending on the rules.

Modern Linux systems may use `nftables` instead.

### Comparison

| Feature | AWS route table | Linux `iptables` |
|---|---|---|
| Main purpose | Selects the traffic's next hop | Filters packets and can perform NAT |
| Operates on | AWS network routing | Linux host networking |
| Example | Direct traffic to a NAT Gateway | Allow or block TCP port 22 |
| Replaces the other? | No | No |

### Troubleshooting example
If an EC2 instance cannot connect to the internet, check its route table, security group, network ACL, Linux firewall, and application configuration.

**Remember:** Route tables decide where traffic goes. Firewalls control which traffic is allowed or blocked.

## 22. What Is VPC Peering?

### Definition
**VPC peering** is a networking connection that allows two VPCs to communicate privately using private IP addresses, subject to routing and security configuration.

VPCs may be in the same AWS account or different accounts. Supported peering configurations can also connect VPCs across Regions.

### Why do we use VPC peering?
- To allow applications in different VPCs to communicate.
- To connect application and service networks.
- To transfer data privately between connected VPCs.
- To avoid sending that inter-VPC traffic over the public internet.

### Example
- VPC A CIDR: `10.0.0.0/16`
- VPC B CIDR: `172.16.0.0/16`

If we create a peering connection and configure routes and security rules, an application in VPC A can communicate with a database in VPC B using private IP addresses.

### Basic setup
1. Create a VPC peering connection request.
2. Accept it from the other VPC or account when required.
3. Add a route in VPC A's relevant route table to VPC B's CIDR, targeting the peering connection.
4. Add a corresponding route in VPC B to VPC A's CIDR.
5. Configure security groups and network ACLs.
6. Test the connection using the required application protocol.

### Important points
- VPC peering works between VPCs in the same or different AWS accounts, subject to configuration.
- Peered VPCs must have non-overlapping IP address ranges for the connection to be established.
- A peering connection alone does not guarantee application connectivity.
- Both sides need appropriate routes and security rules.

## 23. Is VPC Peering One-Way or Two-Way?

VPC peering supports communication in both directions. It is not inherently one-way.

However, routing and security must be configured appropriately on both sides.

### Example
VPC A is peered with VPC B.
- VPC A has a route to VPC B.
- VPC B has a route to VPC A.

If the security rules permit the traffic, resources in either VPC can initiate connections to resources in the other VPC.

This does not mean every connection is automatically allowed. Security groups, network ACLs, host firewalls, and application rules still apply.

### What is transitive peering?

VPC peering does **not** support transitive routing.

Suppose:
- VPC A is peered with VPC B.
- VPC A is peered with VPC C.

VPC B cannot use VPC A as an intermediate router to reach VPC C through those peering connections.

To connect B and C, establish a supported network path, such as direct peering between B and C, or use a suitable architecture such as AWS Transit Gateway.

```text
        VPC B
          |
      Peering 1
          |
        VPC A
          |
      Peering 2
          |
        VPC C
```

In this example, A can communicate with B and C, but B cannot automatically reach C through A.

### Does SSH or SCP create VPC peering?
No.
- **VPC peering** establishes network connectivity between VPCs.
- **SSH** provides secure remote access over an existing network path.
- **SCP** copies files securely over SSH.

After peering, routing, and security rules are configured, SSH or SCP may be used between suitable resources in the peered VPCs.

## 24. How Do Two VPCs Communicate?

Suppose an application server in VPC A needs to connect to a database in VPC B.

1. The application identifies the database's private IP address or resolves its hostname.
2. The source instance sends traffic toward the database IP.
3. The source subnet's route table directs the traffic through the peering connection.
4. The peering connection carries traffic to the other VPC.
5. The destination VPC's routing configuration provides a path to the database subnet.
6. The database security group allows the required inbound traffic.
7. The database sends its response over the appropriate return path.

### Requirements
- An active VPC peering connection.
- Non-overlapping CIDR ranges.
- Correct routes on both sides.
- Appropriate security group rules.
- Appropriate network ACL rules.
- A running destination service listening on the required port.
- Correct DNS configuration if hostnames are used.

**Remember:** A peering connection alone does not guarantee that applications in the two VPCs can communicate.

## 25. Example AWS VPC Architecture

```text
                         INTERNET USERS
                               |
                            Internet
                               |
                     +-------------------+
                     | Internet Gateway  |
                     +-------------------+
                               |
              +----------------------------------+
              |              AWS VPC             |
              |          10.0.0.0/16              |
              |                                  |
              |    +-------------------------+   |
              |    |     Public Subnet       |   |
              |    |       10.0.1.0/24       |   |
              |    |                         |   |
              |    |  EC2 Application Server  |   |
              |    +-------------------------+   |
              |                |                 |
              |                | Local VPC route |
              |                v                 |
              |    +-------------------------+   |
              |    |     Private Subnet      |   |
              |    |      10.0.11.0/24       |   |
              |    |                         |   |
              |    |     Database Server     |   |
              |    +-------------------------+   |
              |                                  |
              |    +-------------------------+   |
              |    |     Public Subnet       |   |
              |    |       NAT Gateway       |   |
              |    +-------------------------+   |
              +----------------------------------+
```

### How it works

**Incoming traffic:** Internet users can connect to the public application server through the IGW when the routes, public IP addressing, and security rules permit it.

**Application-to-database traffic:** The application server communicates with the database using the VPC's local routing, provided the security rules and database configuration permit the connection.

**Private subnet outbound traffic:** If a private resource needs outbound IPv4 internet connectivity, its subnet can route eligible traffic through the NAT Gateway.

**Important:** A NAT Gateway is not required for application-to-database communication within the same VPC when the local route provides connectivity. In production, keep databases private and allow database traffic only from the required application resources.

## 26. Important Differences for Interviews

### IGW vs. NAT Gateway

| Feature | Internet Gateway | NAT Gateway |
|---|---|---|
| Main purpose | Enables VPC internet communication | Enables outbound IPv4 connections from private resources |
| Attachment/location | Attached to a VPC | Deployed in a subnet |
| Typical use | Public internet connectivity | Private subnet outbound connectivity |
| Public IP on private EC2 required? | Generally required for direct public IPv4 connectivity | No |
| Allows unsolicited inbound connections to private EC2 through it? | Depends on public resource configuration and security controls | No, not through the NAT Gateway |
| Typical route | Public subnet route table → IGW | Private subnet route table → NAT Gateway |

### VPC Peering vs. Internet Gateway

| Feature | VPC peering | Internet Gateway |
|---|---|---|
| Purpose | Connects two VPCs privately | Enables VPC internet connectivity |
| Communication | Between peered VPCs | Between a VPC and the internet |
| Public internet required? | No | Yes, for internet communication |
| Routes required? | Yes | Yes |
| Security configuration required? | Yes | Yes |

### VPC Peering vs. VPC Endpoint

| Feature | VPC peering | VPC endpoint |
|---|---|---|
| Main purpose | Connects two VPCs | Provides private access to supported AWS services or endpoint services |
| Typical use | An application in one VPC accesses a resource in another VPC | A private EC2 instance accesses S3 |
| Routing | Requires appropriate routes | Depends on endpoint type |
| IGW required for this traffic? | Not inherently | Not inherently |

## 27. AWS Networking Troubleshooting

### Scenario 1: EC2 cannot access the internet
Check:
1. Is the IGW attached to the VPC?
2. Does the subnet's route table have a route to the IGW?
3. Does the instance have a public IPv4 address or Elastic IP?
4. Do security groups permit the required traffic?
5. Do network ACLs permit the traffic and return traffic?
6. Does the operating system firewall allow the connection?
7. Is the destination service reachable?

### Scenario 2: Private EC2 cannot download packages
Check:
1. Is there a NAT Gateway or another suitable network path?
2. Is the NAT Gateway in a public subnet?
3. Does the public subnet's route table point to the IGW?
4. Does the private subnet's route table point to the NAT Gateway?
5. Are security rules and network ACLs appropriate?
6. Does DNS resolution work?
7. Is the external repository reachable?

### Scenario 3: EC2 cannot connect to RDS
Check:
1. Is the database endpoint correct?
2. Does DNS resolve the endpoint?
3. Can the EC2 instance reach the database subnet?
4. Does the RDS security group allow inbound traffic from the application server's security group?
5. Is the database listening on the expected port?
6. Are network ACLs and host-level controls allowing the connection?
7. Is the database available?

### Scenario 4: Two VPCs cannot communicate
Check:
1. Is the peering connection active?
2. Do the VPCs have non-overlapping CIDR ranges?
3. Does the source route table target the peering connection?
4. Does the destination VPC have the required return route?
5. Do security groups allow the required traffic?
6. Are network ACLs configured appropriately?
7. Is the destination application running and listening on the correct port?

## 28. Quick Revision Definitions

- **VPC:** A logically isolated virtual network in AWS.
- **Region:** A geographical area where AWS operates its cloud infrastructure.
- **Availability Zone:** An isolated location within an AWS Region.
- **Subnet:** A range of IP addresses within a VPC that belongs to one AZ.
- **Route:** A rule identifying where matching network traffic should be sent.
- **Route table:** A collection of routing rules associated with a subnet or another supported network attachment.
- **Internet Gateway:** An AWS component that enables VPC internet communication when the necessary configuration is in place.
- **NAT Gateway:** A component that allows private IPv4 resources to initiate outbound connections to external destinations.
- **Egress-Only Internet Gateway:** An IPv6 gateway that permits outbound internet connections without allowing new inbound connections through that gateway.
- **VPC Endpoint:** A component providing private connectivity to supported AWS services or endpoint services.
- **DNS:** A naming system that associates domain names with IP addresses and other information.
- **DNS resolution:** The process of looking up DNS records for a hostname.
- **TCP:** A connection-oriented transport protocol providing reliable, ordered data delivery.
- **UDP:** A connectionless transport protocol that does not guarantee delivery or ordering.
- **Proxy server:** An intermediary that forwards requests between clients and other servers.
- **Peer-to-peer:** A communication model in which systems participate as peers.
- **Unicast:** One sender communicates with one specific receiver.
- **Broadcast:** One sender sends data to all devices in the relevant broadcast domain.
- **VPC peering:** A private networking connection between two VPCs that supports communication in both directions when routing and security rules permit it.

## 29. Final Revision Questions

Try answering these in your own words:

1. What is a VPC, and why do we create one?
2. What is the difference between a Region and an Availability Zone?
3. What is the difference between a public subnet and a private subnet?
4. What is a route, and what is a route table?
5. Why do we attach an IGW to a VPC?
6. Why might a private subnet need a NAT Gateway?
7. What is the difference between a NAT Gateway and an Egress-Only Internet Gateway?
8. What are VPC endpoints, and when would we use them?
9. What is DNS resolution?
10. What are the seven OSI layers?
11. What is the difference between TCP and UDP?
12. What is the difference between a proxy and VPC peering?
13. How can two VPCs communicate privately?
14. Why is VPC peering non-transitive?
15. What is the difference between an AWS route table and a Linux firewall?

### Final tip

For every networking concept, ask three questions:

1. **Where does the traffic come from?**
2. **Where should the traffic go?**
3. **What might block the traffic?**

Understanding these questions will help you learn AWS networking and troubleshoot real-world DevOps problems.
