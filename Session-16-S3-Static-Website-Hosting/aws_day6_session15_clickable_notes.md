# AWS Day 6 --- Session 15 Notes

## Amazon S3, Lifecycle Management, Encryption, and CloudFront

> **How to use these notes:** Click a topic in the table of contents to
> jump directly to its explanation. In most Markdown viewers, click the
> topic title to return to the relevant section.

## Table of Contents

1.  [What is Amazon S3?](#1-what-is-amazon-s3)
2.  [S3 Buckets: General Purpose vs
    Directory](#2-s3-buckets-general-purpose-vs-directory)
3.  [What is an S3 Object?](#3-what-is-an-s3-object)
4.  [What are S3 Storage Classes?](#4-what-are-s3-storage-classes)
5.  [What is S3 Lifecycle
    Management?](#5-what-is-s3-lifecycle-management)
6.  [What is S3 Encryption?](#6-what-is-s3-encryption)
7.  [How Do We Control Access to an S3
    Bucket?](#7-how-do-we-control-access-to-an-s3-bucket)
8.  [How Can We Monitor S3 Activity?](#8-how-can-we-monitor-s3-activity)
9.  [S3 Bucket Naming Rules](#9-s3-bucket-naming-rules)
10. [Can We Change an S3 Bucket
    Name?](#10-can-we-change-an-s3-bucket-name)
11. [Can We Change an S3 Bucket
    Region?](#11-can-we-change-an-s3-bucket-region)
12. [How Many S3 Buckets Can We
    Create?](#12-how-many-s3-buckets-can-we-create)
13. [How Do We Delete an S3 Bucket?](#13-how-do-we-delete-an-s3-bucket)
14. [What is Amazon CloudFront?](#14-what-is-amazon-cloudfront)
15. [What are IAM and CloudFront
    OAC?](#15-what-are-iam-and-cloudfront-oac)
16. [Practical Project: S3 + CloudFront with
    Terraform](#16-practical-project-s3--cloudfront-with-terraform)
17. [Terraform Error Encountered in the
    Lab](#17-terraform-error-encountered-in-the-lab)
18. [Quick Revision](#18-quick-revision)
19. [Interview Practice Questions](#19-interview-practice-questions)

------------------------------------------------------------------------

## 1. What is Amazon S3?

**Amazon S3 (Simple Storage Service)** is an AWS object storage service
used to store and retrieve data such as images, videos, documents,
backups, application files, and log files.

### Why do we use S3?

-   To store files securely and reliably.
-   To store backups and application data.
-   To host static website files.
-   To store logs, images, videos, and documents.
-   To provide data to other AWS services.

### Real-world example

An e-commerce website can store product images in S3. The website can
retrieve those images when customers view products.

## 2. S3 Buckets: General Purpose vs Directory

An **S3 bucket** is a container used to store objects in Amazon S3.

Amazon S3 provides different bucket types for different workloads.

### General purpose buckets

-   Suitable for most S3 workloads.
-   Store data redundantly across multiple Availability Zones in the
    selected AWS Region.
-   Commonly used for backups, website files, application data, logs,
    and data lakes.

### Directory buckets

-   Used with the S3 Express One Zone storage class for
    high-performance, very low-latency workloads.
-   Store data in a single Availability Zone.
-   Availability-zone-level resilience differs from general purpose
    buckets. If that Availability Zone has an outage, data access may be
    affected.
-   Best suited to workloads that need very low latency and are designed
    for this availability model.

### Key difference

General purpose buckets are the normal choice for most workloads.
Directory buckets are designed for specific high-performance use cases.
Choose based on performance, availability, and resilience needs.

## 3. What is an S3 Object?

An **S3 object** is the file stored in a bucket, along with its metadata
and an object key used to identify it.

Examples include: - `photo.jpg` - `backup.zip` - `application.log` -
`reports/january.pdf`

The object key identifies the object within the bucket. Folder-like
paths shown in the console are generally represented through prefixes in
object keys.

## 4. What are S3 Storage Classes?

**S3 storage classes** are options designed for different access
patterns, retrieval needs, availability requirements, and storage costs.

### Common storage classes

-   **S3 Standard:** For frequently accessed data.
-   **S3 Intelligent-Tiering:** Automatically moves eligible data
    between access tiers as access patterns change, subject to its rules
    and monitoring charges.
-   **S3 Standard-IA:** For data accessed less often but needed quickly
    when requested.
-   **S3 One Zone-IA:** For infrequently accessed data stored in one
    Availability Zone.
-   **S3 Glacier Instant Retrieval:** For archive data that still needs
    millisecond retrieval.
-   **S3 Glacier Flexible Retrieval:** For archive data where retrieval
    can take longer.
-   **S3 Glacier Deep Archive:** For long-term archive data that is
    rarely accessed and can tolerate longer retrieval times.

You can choose an appropriate storage class when uploading an object,
where supported. You can also configure lifecycle rules to transition
eligible objects to another storage class later.

## 5. What is S3 Lifecycle Management?

**S3 Lifecycle Management** lets you create rules that automatically
transition objects between storage classes or expire objects after a
specified period.

### Why do we use it?

-   To reduce storage costs.
-   To move older or less frequently accessed data to lower-cost
    storage.
-   To automatically remove temporary or expired data.
-   To apply consistent data-retention practices.

### Real-world example

A company stores application logs in S3: 1. Keep new logs in S3 Standard
for 30 days. 2. Transition eligible logs to an archive storage class
after 30 days. 3. Expire logs after the required retention period, if
allowed by company policy.

### Important point

Lifecycle actions depend on the storage class, object age, minimum
storage-duration rules, versioning, and other S3 rules. Test expiration
rules carefully before applying them to important data.

## 6. What is S3 Encryption?

**Encryption** converts readable data into a protected form. Appropriate
keys and permissions are needed to decrypt and access encrypted data.

Amazon S3 supports encryption at rest. For most new object uploads, S3
automatically applies server-side encryption with Amazon S3 managed keys
(**SSE-S3**) by default.

### Common encryption options

-   **SSE-S3:** Amazon S3 manages the encryption keys.
-   **SSE-KMS:** AWS Key Management Service (KMS) manages the keys and
    provides additional key-management controls. KMS use may incur
    charges.
-   **SSE-C:** The customer supplies an encryption key with the request.
-   **Client-side encryption:** The application encrypts data before
    uploading it to S3.

### Does encryption make an object private?

Not by itself. Encryption protects data, but access must also be
controlled using IAM permissions, bucket policies, Block Public Access
settings, and other security controls.

## 7. How Do We Control Access to an S3 Bucket?

S3 access controls determine who can perform actions such as listing a
bucket, uploading objects, reading objects, or deleting objects.

### Common controls

-   **IAM policies:** Define what an IAM user or role is allowed to do.
-   **Bucket policies:** Policies attached directly to an S3 bucket.
-   **S3 Block Public Access:** Helps prevent unintended public access.
-   **Access points:** Provide named endpoints with policies for
    specific access patterns.
-   **Object Ownership and ACLs:** Control ownership and, where enabled,
    access-control lists.

### Security tip

Keep Block Public Access enabled unless you have a clear and reviewed
requirement for public access. For a private S3 origin served through
CloudFront, consider Origin Access Control (OAC).

## 8. How Can We Monitor S3 Activity?

Different AWS features record or monitor different kinds of S3 activity.

-   **AWS CloudTrail:** Records supported S3 API activity, depending on
    the trail or event data store configuration.
-   **S3 server access logging:** Provides request records for a bucket.
    Delivery may not be immediate.
-   **Amazon CloudWatch:** Supports relevant metrics, alarms, and
    monitoring integrations.

Not every activity is automatically recorded in one universal log.
Configure the appropriate feature for the information you need.

## 9. S3 Bucket Naming Rules

For **general purpose buckets**, names must: - Be globally unique across
AWS accounts within the AWS partition. - Follow S3 naming rules. - Use
permitted characters, generally lowercase letters, numbers, hyphens, and
periods. - Be between 3 and 63 characters long. - Start and end with a
letter or number. - Not be formatted like an IP address.

Bucket names can appear in URLs, so a bucket name should not be treated
as a secret.

**Important:** Directory buckets have different naming rules, including
a zone-related suffix.

## 10. Can We Change an S3 Bucket Name?

No. You cannot rename an existing S3 bucket.

If you need a different name: 1. Create a new bucket with the required
name. 2. Copy or replicate the objects to the new bucket. 3. Update
applications, policies, and configurations that refer to the old bucket.
4. Verify that the new setup works before removing the old bucket.

For general purpose buckets, the name must be unique across the AWS
partition, not merely within your own account.

## 11. Can We Change an S3 Bucket Region?

No. You cannot change the Region of an existing S3 bucket.

To move data to another Region: 1. Create a new bucket in the
destination Region. 2. Copy the objects or configure replication. 3.
Update applications and permissions to use the destination bucket. 4.
Verify the data and application before retiring the old bucket.

Consider data-transfer costs, replication settings, and any compliance
requirements.

## 12. How Many S3 Buckets Can We Create?

AWS sets account-level bucket quotas. The default quota for general
purpose buckets is commonly 100 per account, and it may be possible to
request an increase. Check the current value in AWS Service Quotas
because quotas can change and may differ by bucket type.

## 13. How Do We Delete an S3 Bucket?

An S3 bucket must be empty before it can be deleted.

If versioning is enabled, removing only the current objects may not
empty the bucket. Noncurrent object versions and delete markers may also
need to be removed.

### AWS CLI example

The following command recursively removes current objects from a bucket:

``` bash
aws s3 rm s3://YOUR-BUCKET-NAME --recursive
```

Replace `YOUR-BUCKET-NAME` with the intended bucket name only after
checking it carefully.

**Caution:** - This command deletes current objects recursively; do not
run it on data you need. - It does not, by itself, permanently remove
every historical version in a versioned bucket. - It does not delete the
bucket itself. - Large buckets and versioned buckets may require a
carefully planned CLI or lifecycle-based cleanup process.

You can delete the bucket after it has been emptied and any remaining
versions or delete markers have been handled.

## 14. What is Amazon CloudFront?

**Amazon CloudFront** is AWS's Content Delivery Network (CDN). It caches
and delivers content from locations closer to viewers, helping reduce
latency and repeated requests to the origin.

### Why do we use CloudFront?

-   To deliver website content faster to users in different locations.
-   To cache static files such as images, CSS, JavaScript, and
    downloads.
-   To reduce repeated requests to an origin such as S3.
-   To support HTTPS delivery.
-   To integrate with security features such as AWS WAF and signed URLs
    or cookies when configured.

CloudFront improves content delivery, but it does not guarantee that a
site will remain available under every traffic spike. The origin, cache
behavior, quotas, and configuration still matter.

## 15. What are IAM and CloudFront OAC?

### What is IAM?

**AWS Identity and Access Management (IAM)** helps control who can
access AWS resources and which actions they can perform.

For example, an IAM role can allow Terraform to create certain AWS
resources without using a user's long-term access keys.

### What is Origin Access Control (OAC)?

**Origin Access Control (OAC)** is a CloudFront feature that lets
CloudFront securely access an S3 origin. It can sign requests to S3 so
the bucket can remain private while CloudFront delivers authorized
content.

OAC is the recommended modern option for many S3 origins and replaces
the older Origin Access Identity (OAI) approach in supported
configurations.

### Basic setup for a private S3 origin

1.  Configure the CloudFront distribution to use OAC.
2.  Configure the S3 bucket policy to allow the intended CloudFront
    distribution to read the required objects.
3.  Keep public access blocked unless public access is explicitly
    required.
4.  Test the CloudFront URL after deployment.

## 16. Practical Project: S3 + CloudFront with Terraform

The goal of this lab was to automate static website hosting with Amazon
S3, CloudFront, and Terraform.

### Typical workflow

1.  Configure the AWS provider and required variables.
2.  Create an S3 bucket.
3.  Configure the website files or upload objects.
4.  Configure CloudFront with the S3 origin.
5.  Configure OAC and the appropriate bucket policy.
6.  Run `terraform init`.
7.  Run `terraform plan` to review the proposed changes.
8.  Run `terraform apply` to create the resources.
9.  Test the website using the CloudFront distribution URL.

## 17. Terraform Error Encountered in the Lab

### Error message

``` text
Error: creating CloudFront Distribution
StatusCode: 403
AccessDenied: Your account must be verified before you can add new CloudFront resources.
```

### What does it mean?

Terraform reached AWS, but AWS rejected the request to create the
CloudFront distribution because the account needs verification.

This is an AWS account restriction, not necessarily a Terraform syntax
or project-code error. Repeatedly running `terraform apply` is unlikely
to resolve the specific restriction.

### What should you do?

1.  Check the AWS account for verification or billing notices.
2.  Contact AWS Support through the Support Center and ask what
    verification is required.
3.  Include the exact error and Request ID from the failed request.
4.  After AWS confirms the restriction is resolved, review the Terraform
    output and run `terraform apply` again if appropriate.

**Lab status:** A support case was raised with AWS. The website output
could not be verified because CloudFront distribution creation was
blocked.

## 18. Quick Revision

-   **Amazon S3:** Object storage service for files, backups, logs,
    website assets, and other data.
-   **Bucket:** Container for S3 objects.
-   **Object:** File plus its metadata and object key.
-   **Lifecycle Management:** Rules that transition or expire objects
    automatically.
-   **S3 encryption:** Protects data at rest; access permissions are
    still required.
-   **General purpose bucket:** The standard choice for most S3
    workloads, with multi-AZ resilience.
-   **Directory bucket:** Designed for S3 Express One Zone workloads
    requiring very low latency.
-   **Storage class:** An option that balances storage cost, access
    frequency, retrieval, and availability.
-   **CloudFront:** CDN that caches and delivers content closer to
    users.
-   **IAM:** Controls identities, permissions, and actions.
-   **OAC:** Lets CloudFront securely access a private S3 origin.
-   **Terraform:** Infrastructure as Code tool used to provision and
    manage AWS resources.

## 19. Interview Practice Questions

1.  What is Amazon S3, and what is the difference between a bucket and
    an object?
2.  What is S3 Lifecycle Management, and why do we use it?
3.  What is the difference between S3 Standard and S3 Standard-IA?
4.  What is the difference between general purpose buckets and directory
    buckets?
5.  Does encryption alone make an S3 object private?
6.  What is the difference between SSE-S3 and SSE-KMS?
7.  Can you change an S3 bucket name or Region after creation?
8.  What is Amazon CloudFront, and why would you use it with S3?
9.  What is CloudFront OAC, and why is it useful for a private S3
    bucket?
10. What should you investigate if Terraform receives `403 AccessDenied`
    while creating a CloudFront distribution?

------------------------------------------------------------------------

[Back to Table of Contents](#table-of-contents)
