# Session 14/55 --- Amazon S3 (Simple Storage Service)

## 1. What is Amazon S3?

**Amazon Simple Storage Service (Amazon S3)** is an AWS object-storage
service used to store and retrieve data over the internet through
authorized requests.

We can store files such as: - Images and videos - Documents, PDFs, and
backups - Application logs and data exports - Static website files -
Data used for analytics

S3 is designed for high durability, scalability, and flexible access
control. Objects can be accessed privately by default, or shared
publicly when there is a valid use case and the bucket's settings and
policies allow it.

### What does "object storage" mean?

In S3, a file is stored as an **object**. An object contains: -
**Data:** the contents of the file - **Key:** the object's name/path
within the bucket - **Metadata:** information about the object, such as
content type and last-modified time

For example, `photos/family.jpg` can be an object key. The slash in the
key looks like a folder path, but S3 stores objects in a flat object
namespace; the console displays prefixes as folders for convenience.

### What does "unstructured data" mean?

Unstructured data is data that does not follow a fixed table-like
format, such as photos, videos, PDFs, audio, and many log files. S3 can
also store structured or semi-structured data, such as CSV, JSON, and
Parquet files. S3 stores the files; another service or application may
be needed to analyze their contents.

## 2. Why do we use S3?

We use S3 when we need durable, scalable storage that applications and
people can access when authorized.

Common uses: - **Backups and disaster recovery:** keep copies of
important files and data. - **Application storage:** store uploaded
images, documents, and other user files. - **Log storage:** retain
application, server, or audit logs. - **Static website hosting:** store
HTML, CSS, JavaScript, and images for a static site. - **Data lakes and
analytics:** keep large datasets for tools such as Amazon Athena. -
**Archive storage:** retain data that is rarely accessed.

## 3. How much data can S3 store?

S3 provides **virtually unlimited total storage** for practical use; you
do not need to pre-allocate a fixed storage capacity for a bucket.

-   A single S3 object can be up to **50 TB** when uploaded using the
    supported multipart upload process.
-   A single `PutObject` upload request supports objects up to **5 GB**.
    Larger objects must use multipart upload.
-   Charges depend on factors such as storage class, amount stored,
    requests, retrieval, and data transfer.

> **Correction:** "5 GB per object" is not the overall S3 object-size
> limit. It is the limit for a single `PutObject` request.

## 4. Durability vs. availability

These terms mean different things:

-   **Durability:** how likely stored data is to remain intact over
    time. S3 Standard is designed for **99.999999999% (11 nines) annual
    object durability**.
-   **Availability:** how often a service is available for requests. S3
    Standard is designed for **99.99% availability** over a given year.

Durability does not mean "no lag," and availability does not guarantee
that every request will have zero delay. No storage system should be
treated as a substitute for appropriate backups, access controls, and
recovery planning.

## 5. S3 terminology

### Bucket

A **bucket** is a container that holds S3 objects. Bucket names must be
unique within the AWS partition's bucket namespace and follow AWS naming
rules. When creating a bucket, you select a Region and configure
relevant options.

### Object

An **object** is the data stored in S3, together with its key and
metadata. For example, `reports/january.pdf` is an object key for a PDF
file.

### Key

The **key** is the full name used to identify an object inside a bucket.
For example, `logs/app/server.log`.

### Region

The **Region** is the AWS geographic area where the bucket is created.
Choose a Region based on latency, compliance, service availability, and
cost needs. S3 manages the underlying infrastructure.

### S3 endpoint and URL

AWS manages S3 service endpoints and DNS infrastructure. A bucket and
its objects can be addressed through an S3 endpoint/URL, depending on
the API and access method. The exact URL format can vary.

A URL alone does **not** make an object public. Access is controlled by
IAM permissions, bucket policies, Block Public Access settings, and
other applicable controls.

## 6. S3 storage classes

Storage classes help balance cost, access frequency, retrieval speed,
and resilience. Choose a class based on how often the data is accessed
and how quickly it must be retrieved.

### S3 Standard

-   **Simple definition:** General-purpose storage for frequently
    accessed data.
-   **Use it for:** Active application files, frequently viewed images,
    website assets, and data accessed regularly.
-   **Availability Zones:** Stores data redundantly across multiple
    Availability Zones.
-   **Cost note:** Good for frequently accessed data, but may cost more
    than archive classes for long-term storage.

### S3 Intelligent-Tiering

-   **Simple definition:** A storage class that automatically moves
    eligible objects between access tiers as access patterns change.
-   **Use it for:** Data whose access frequency is unknown or changes
    over time.
-   **How it works:** S3 monitors access patterns and moves objects
    between tiers according to its rules.
-   **Cost note:** There is a small monthly monitoring and automation
    charge per eligible object. Some optional archive tiers have
    additional considerations and retrieval behavior.

### S3 Standard-Infrequent Access (S3 Standard-IA)

-   **Simple definition:** For data accessed less frequently but needed
    quickly when requested.
-   **Use it for:** Backups, disaster-recovery files, and older reports
    that still need fast access.
-   **Availability Zones:** Designed for data stored redundantly across
    multiple Availability Zones.
-   **Cost note:** Storage cost is lower than Standard, but retrieval
    charges apply. It has a minimum storage duration and minimum
    billable object size.

### S3 One Zone-Infrequent Access (S3 One Zone-IA)

-   **Simple definition:** For infrequently accessed data stored in a
    single Availability Zone.
-   **Use it for:** Re-creatable secondary copies, such as certain
    intermediate files or backups that can be recreated from another
    source.
-   **Availability Zones:** Data is stored in one Availability Zone
    rather than being replicated across multiple Availability Zones by
    this storage class.
-   **Risk and cost:** It costs less than Standard-IA, but data may be
    lost if that Availability Zone is destroyed. Do not use it as the
    only copy of critical data.

### S3 Glacier Flexible Retrieval

-   **Simple definition:** Lower-cost storage for archive data that is
    not accessed often.
-   **Use it for:** Long-term backups and archived records.
-   **Retrieval:** Depending on the retrieval option, access can take
    minutes to hours. It is not intended for data that must always be
    immediately available.
-   **Cost note:** Storage costs are low, but retrieval charges and
    minimum storage-duration rules may apply.

### S3 Glacier Deep Archive

-   **Simple definition:** A very low-cost storage class for data that
    is rarely accessed and can wait to be restored.
-   **Use it for:** Long-term retention, compliance archives, and
    records that may be needed only in exceptional circumstances.
-   **Retrieval:** Usually takes hours; it is not designed for immediate
    access.
-   **Cost note:** Typically among the lowest-cost S3 storage options,
    with retrieval charges and a long minimum storage duration.

### Quick comparison

  -------------------------------------------------------------------------------
  Storage class         Best for          Typical access        Resilience note
                                          expectation           
  --------------------- ----------------- --------------------- -----------------
  S3 Standard           Frequently        Immediate access      Multiple
                        accessed data                           Availability
                                                                Zones

  S3                    Changing or       Immediate access in   Designed for
  Intelligent-Tiering   unknown access    frequent/infrequent   multi-AZ
                        patterns          tiers                 resilience in its
                                                                standard tiers

  S3 Standard-IA        Infrequently      Immediate access      Multiple
                        accessed data                           Availability
                        needing fast                            Zones
                        access                                  

  S3 One Zone-IA        Infrequent,       Immediate access      One Availability
                        re-creatable data                       Zone

  S3 Glacier Flexible   Archives accessed Minutes to hours,     Designed for
  Retrieval             occasionally      depending on          resilient archive
                                          retrieval option      storage

  S3 Glacier Deep       Rarely accessed   Usually hours         Designed for
  Archive               long-term                               resilient archive
                        archives                                storage
  -------------------------------------------------------------------------------

*Exact pricing, minimum-duration rules, and retrieval times depend on
the storage class, Region, and selected retrieval option. Check AWS
documentation and pricing for the workload before choosing a class.*

## 7. How to create an S3 bucket

1.  Sign in to the AWS Management Console.
2.  Open **Amazon S3**.
3.  Select **Create bucket**.
4.  Enter a globally unique bucket name.
5.  Select the AWS Region.
6.  Review settings such as Object Ownership, encryption, versioning,
    and Block Public Access.
7.  Create the bucket.
8.  Open the bucket and choose **Upload**.
9.  Add files or folders and start the upload.

## 8. How to upload files and folders

-   Open the bucket in the S3 console.
-   Choose **Upload**.
-   Add files or select a folder, depending on the console and browser
    support.
-   Start the upload and confirm that it completes.
-   You can also upload through AWS CLI, SDKs, or an application with
    the required permissions.

Folders shown in the console are usually represented by object-key
prefixes, rather than traditional folders on a disk.

## 9. Making an S3 bucket or object public --- important security note

In today's practical, you created an S3 bucket, uploaded files, and
worked on making the bucket publicly accessible.

**Public access should be enabled only when the data is intended for
everyone**, such as public website images or public downloads. Never
expose private documents, credentials, customer data, logs containing
sensitive information, or backups.

Before enabling public access: - Confirm that the files are meant to be
public. - Understand that account- and bucket-level **Block Public
Access** settings can prevent public policies from working. - Use the
narrowest possible permissions; avoid granting public access to all
objects unless that is truly required. - For private files, keep Block
Public Access enabled and use IAM permissions or time-limited presigned
URLs. - Follow your organization's security policy and review access
regularly.

A public bucket is not required for normal S3 use. Most buckets should
remain private.

## 10. How S3 is used in real DevOps projects

-   **CI/CD artifacts:** store build packages and release artifacts.
-   **Application logs:** keep logs for troubleshooting, auditing, or
    later analysis.
-   **Backups:** store database exports and recovery copies.
-   **Static assets:** host or distribute images, scripts, and website
    files.
-   **Terraform state:** store Terraform state remotely when configured
    with appropriate access controls, versioning, and locking support.
-   **Data and reports:** keep deployment reports, test results, and
    monitoring exports.

For production use, apply least-privilege IAM permissions, encryption,
versioning where appropriate, lifecycle policies, monitoring, and
recovery controls.

## 11. Session 14 practical summary

In this session, I: - Learned the purpose of Amazon S3 and object
storage. - Learned the difference between buckets, objects, keys,
Regions, and endpoints. - Reviewed common S3 storage classes and their
use cases. - Created an S3 bucket. - Uploaded files to the bucket. -
Practiced configuring public access, while learning why public access
must be handled carefully.

## 12. Quick interview revision

**Q: What is Amazon S3?**\
Amazon S3 is AWS object storage used to store and retrieve data at
scale.

**Q: What is a bucket?**\
A bucket is a container for S3 objects.

**Q: What is an object?**\
An object is stored data identified by a key and associated metadata.

**Q: Is S3 storage capacity fixed?**\
No. S3 scales automatically; users do not pre-allocate a fixed bucket
capacity.

**Q: What is the difference between durability and availability?**\
Durability describes protection against data loss. Availability
describes how often the service is available for requests.

**Q: Which storage class is suitable for frequently accessed data?**\
S3 Standard.

**Q: Which class is useful when access patterns change?**\
S3 Intelligent-Tiering.

**Q: What is the key difference between Standard-IA and One Zone-IA?**\
Standard-IA stores data redundantly across multiple Availability Zones;
One Zone-IA stores it in one Availability Zone.

**Q: Which class is suitable for very long-term archives?**\
S3 Glacier Deep Archive, when waiting hours for retrieval is acceptable.

**Q: Does an S3 URL make an object public?**\
No. Permissions and public-access settings determine whether a request
is allowed.

------------------------------------------------------------------------

**Learning note:** Storage-class behavior, pricing, and service limits
can change. For production decisions, verify the current AWS S3
documentation and regional pricing.
