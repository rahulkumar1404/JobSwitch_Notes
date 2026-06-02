# ☁️ AWS (Amazon Web Services) - Complete Notes: Zero to Advanced

> **Hindi Explanation + English Interview Answers**  
> Reference: [TutorialsPoint AWS Tutorial](https://www.tutorialspoint.com/amazon_web_services/index.htm)

---

## 📑 Table of Contents

1. [Cloud Computing Basics](#1-cloud-computing-basics)
2. [AWS Introduction](#2-aws-introduction)
3. [AWS Global Infrastructure](#3-aws-global-infrastructure)
4. [AWS Management Console](#4-aws-management-console)
5. [IAM (Identity and Access Management)](#5-iam-identity-and-access-management)
6. [Amazon EC2](#6-amazon-ec2-elastic-compute-cloud)
7. [Amazon AMI](#7-amazon-machine-image-ami)
8. [Auto Scaling](#8-auto-scaling)
9. [Elastic Load Balancer (ELB)](#9-elastic-load-balancer-elb)
10. [AWS Lambda](#10-aws-lambda)
11. [Amazon VPC](#11-amazon-vpc-virtual-private-cloud)
12. [Amazon Route 53](#12-amazon-route-53)
13. [AWS Direct Connect](#13-aws-direct-connect)
14. [Amazon S3](#14-amazon-s3-simple-storage-service)
15. [Amazon EBS](#15-amazon-ebs-elastic-block-store)
16. [Amazon EFS](#16-amazon-efs-elastic-file-system)
17. [AWS Storage Gateway](#17-aws-storage-gateway)
18. [Amazon CloudFront](#18-amazon-cloudfront)
19. [Amazon RDS](#19-amazon-rds-relational-database-service)
20. [Amazon DynamoDB](#20-amazon-dynamodb)
21. [Amazon Redshift](#21-amazon-redshift)
22. [Amazon ElastiCache](#22-amazon-elasticache)
23. [Amazon Kinesis](#23-amazon-kinesis)
24. [Amazon EMR](#24-amazon-emr-elastic-mapreduce)
25. [AWS Data Pipeline](#25-aws-data-pipeline)
26. [Amazon Machine Learning](#26-amazon-machine-learning)
27. [Amazon SQS](#27-amazon-sqs-simple-queue-service)
28. [Amazon SNS](#28-amazon-sns-simple-notification-service)
29. [Amazon SES](#29-amazon-ses-simple-email-service)
30. [Amazon SWF](#30-amazon-swf-simple-workflow-service)
31. [Amazon WorkMail](#31-amazon-workmail)
32. [Amazon WorkSpaces](#32-amazon-workspaces)
33. [AWS CloudFormation](#33-aws-cloudformation)
34. [AWS CloudWatch](#34-aws-cloudwatch)
35. [AWS CloudTrail](#35-aws-cloudtrail)
36. [AWS Trusted Advisor](#36-aws-trusted-advisor)
37. [AWS KMS & Security](#37-aws-kms--security)
38. [Amazon API Gateway](#38-amazon-api-gateway)
39. [AWS Elastic Beanstalk](#39-aws-elastic-beanstalk)
40. [Amazon ECS & EKS](#40-amazon-ecs--eks)
41. [AWS CodePipeline (CI/CD)](#41-aws-codepipeline-cicd)
42. [AWS Well-Architected Framework](#42-aws-well-architected-framework)
43. [AWS Pricing & Billing](#43-aws-pricing--billing)

---

## 1. Cloud Computing Basics

### 📖 Hindi Explanation:
Cloud Computing ka matlab hai ki aap apne data aur applications ko internet ke through access karte ho, bina koi physical hardware kharide. Jaise aap Netflix pe movie dekhte ho bina DVD kharide — waise hi cloud pe aap servers, storage, databases use karte ho bina khud manage kiye.

**Cloud Computing ke 3 Models:**
- **IaaS (Infrastructure as a Service):** Aapko raw infrastructure milta hai (servers, network). Example: EC2
- **PaaS (Platform as a Service):** Aapko platform milta hai jahan aap code deploy karte ho. Example: Elastic Beanstalk
- **SaaS (Software as a Service):** Ready-made software milta hai. Example: Gmail, Dropbox

**Deployment Models:**
- **Public Cloud:** Sab ke liye available (AWS, Azure, GCP)
- **Private Cloud:** Sirf ek organization ke liye
- **Hybrid Cloud:** Public + Private ka combination

### 🎯 Interview Q&A:

**Q: What is Cloud Computing?**  
**A:** Cloud Computing is the on-demand delivery of IT resources such as compute power, storage, and databases over the internet with pay-as-you-go pricing. Instead of owning and maintaining physical data centers, you rent access to technology services from a cloud provider like AWS.

**Q: What are the different types of cloud service models?**  
**A:** There are three main service models:
- **IaaS** – Provides virtualized computing resources (e.g., AWS EC2, VPC)
- **PaaS** – Provides a platform for developing and deploying applications (e.g., AWS Elastic Beanstalk)
- **SaaS** – Delivers fully managed software applications (e.g., Amazon WorkMail)

**Q: What are the advantages of Cloud Computing?**  
**A:**
1. Trade capital expense for variable expense
2. Benefit from massive economies of scale
3. Stop guessing capacity
4. Increase speed and agility
5. Go global in minutes
6. No data center maintenance cost

---

## 2. AWS Introduction

### 📖 Hindi Explanation:
AWS (Amazon Web Services) duniya ka sabse bada cloud platform hai jo Amazon ne 2006 mein launch kiya tha. Yeh 200+ services provide karta hai jaise computing, storage, database, machine learning, etc. Netflix, Airbnb, NASA jaise companies AWS use karti hain.

AWS ka fayda yeh hai ki aap sirf utna pay karo jitna use karo (Pay-as-you-go model). Koi upfront cost nahi. Aap 1 server se lekar 1000 servers tak scale kar sakte ho minutes mein.

### 🎯 Interview Q&A:

**Q: What is AWS?**  
**A:** AWS (Amazon Web Services) is a comprehensive cloud computing platform provided by Amazon. It offers over 200 fully-featured services from data centers globally, including compute, storage, networking, database, analytics, AI/ML, IoT, security, and more. It follows a pay-as-you-go pricing model.

**Q: Why do companies prefer AWS over other cloud providers?**  
**A:**
1. Largest market share and most mature platform
2. Broadest and deepest set of services
3. Largest global infrastructure (regions & availability zones)
4. Strong security and compliance certifications
5. Extensive partner ecosystem and community support
6. Flexible pricing with Free Tier for learning

---

## 3. AWS Global Infrastructure

### 📖 Hindi Explanation:
AWS ki global infrastructure 3 main components pe based hai:

- **Region:** Ek geographical area jahan AWS ke data centers hain. Example: Mumbai (ap-south-1), US East (us-east-1). Har region mein multiple AZs hote hain.
- **Availability Zone (AZ):** Ek ya zyada data centers jo ek region mein hote hain. Yeh ek dusre se isolated hote hain taaki agar ek fail ho toh doosra chalta rahe.
- **Edge Locations:** Yeh CloudFront ke liye use hote hain — content ko users ke paas cache karne ke liye.

Jaise agar aap India ke users ke liye app bana rahe ho toh Mumbai region choose karoge taaki latency kam ho.

### 🎯 Interview Q&A:

**Q: What is an AWS Region?**  
**A:** An AWS Region is a physical location in the world consisting of multiple isolated Availability Zones. Each region is completely independent for fault isolation. Example: ap-south-1 (Mumbai), us-east-1 (N. Virginia).

**Q: What is an Availability Zone?**  
**A:** An Availability Zone (AZ) is one or more discrete data centers with redundant power, networking, and connectivity within a Region. AZs are physically separated but connected through low-latency links, enabling high availability architectures.

**Q: What are Edge Locations?**  
**A:** Edge Locations are endpoints for AWS CloudFront used to cache content closer to end users for faster delivery. There are more Edge Locations than Regions (400+ globally).

---

## 4. AWS Management Console

### 📖 Hindi Explanation:
AWS Management Console ek web-based interface hai jahan se aap apni saari AWS services ko manage kar sakte ho. Iske alawa aap AWS CLI (Command Line Interface) aur AWS SDK (Software Development Kit) bhi use kar sakte ho.

- **Console:** Browser pe GUI use karo
- **CLI:** Terminal/Command Prompt se commands run karo
- **SDK:** Programming languages (Python, Java, Node.js) se AWS services access karo

### 🎯 Interview Q&A:

**Q: What are the different ways to access AWS services?**  
**A:** AWS services can be accessed through:
1. **AWS Management Console** – Web-based GUI
2. **AWS CLI** – Command-line tool for scripting
3. **AWS SDKs** – Language-specific APIs (Python/Boto3, Java, Node.js)
4. **AWS CloudFormation** – Infrastructure as Code (IaC)
5. **AWS CDK** – Cloud Development Kit for programmatic infrastructure

---

## 5. IAM (Identity and Access Management)

### 📖 Hindi Explanation:
IAM AWS ka security service hai jo control karta hai ki kaun kya access kar sakta hai. Jaise ghar mein alag-alag chaabiyan hoti hain alag-alag rooms ke liye — waise hi IAM mein alag-alag permissions hoti hain.

**Key Components:**
- **Users:** Individual log in karne wale log (jaise employees)
- **Groups:** Users ka collection (jaise "Developers" group)
- **Roles:** Temporary permissions jo services ya users ko di jaati hain
- **Policies:** JSON documents jo define karte hain ki kya allow/deny hai

**Important Rules:**
- Root account kabhi daily use mein mat karo
- Hamesha MFA (Multi-Factor Authentication) enable karo
- Least Privilege Principle follow karo — sirf utni permission do jitni zaroorat hai

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### 🎯 Interview Q&A:

**Q: What is IAM in AWS?**  
**A:** IAM (Identity and Access Management) is a global AWS service that enables you to manage access to AWS resources securely. It allows you to create and manage users, groups, roles, and policies to control who can do what in your AWS account.

**Q: What is the difference between IAM Role and IAM User?**  
**A:**
- **IAM User** – Has permanent long-term credentials (username/password, access keys). Used by people.
- **IAM Role** – Has temporary security credentials. Used by AWS services, applications, or cross-account access. No permanent credentials.

**Q: What is the Principle of Least Privilege?**  
**A:** It means granting only the minimum permissions required to perform a task. Users and services should not have more access than what they need to function.

**Q: What is an IAM Policy?**  
**A:** An IAM Policy is a JSON document that defines permissions. It specifies what actions are allowed or denied on which AWS resources. Policies can be attached to users, groups, or roles.

---

## 6. Amazon EC2 (Elastic Compute Cloud)

### 📖 Hindi Explanation:
EC2 AWS ka sabse important service hai — yeh aapko virtual servers (instances) deta hai cloud mein. Jaise aap ek computer rent pe lete ho internet pe.

**Instance Types:**
- **General Purpose (t2, t3, m5):** Balanced compute, memory, networking
- **Compute Optimized (c5):** High-performance processing
- **Memory Optimized (r5):** Large databases ke liye
- **Storage Optimized (i3, d2):** High I/O operations
- **GPU Instances (p3, g4):** Machine Learning, gaming

**Pricing Models:**
- **On-Demand:** Jab chaaho use karo, per hour/second pay karo
- **Reserved Instances:** 1 ya 3 saal ka commitment — 75% tak discount
- **Spot Instances:** Unused capacity pe bid karo — 90% tak discount (interrupt ho sakta hai)
- **Dedicated Hosts:** Physical server sirf aapke liye

**Key Concepts:**
- **Security Group:** Virtual firewall jo inbound/outbound traffic control karta hai
- **Key Pair:** SSH se instance mein login karne ke liye
- **Elastic IP:** Static public IP address
- **User Data:** Instance launch hone pe automatically script run karo

### 🎯 Interview Q&A:

**Q: What is Amazon EC2?**  
**A:** Amazon EC2 (Elastic Compute Cloud) is a web service that provides resizable compute capacity in the cloud. It allows you to launch virtual servers (instances), configure security, networking, and manage storage. You pay only for the capacity you use.

**Q: What are EC2 instance types?**  
**A:** EC2 instance types are categorized by use case:
- **General Purpose (T, M series)** – Balanced workloads
- **Compute Optimized (C series)** – CPU-intensive tasks
- **Memory Optimized (R, X series)** – In-memory databases
- **Storage Optimized (I, D series)** – High sequential I/O
- **Accelerated Computing (P, G series)** – ML/GPU workloads

**Q: What is the difference between On-Demand, Reserved, and Spot instances?**  
**A:**
- **On-Demand** – Pay per hour/second, no commitment, highest flexibility
- **Reserved** – 1-3 year commitment, up to 75% discount, predictable workloads
- **Spot** – Bid on unused capacity, up to 90% discount, can be interrupted with 2-min notice

**Q: What is a Security Group?**  
**A:** A Security Group acts as a virtual firewall for EC2 instances controlling inbound and outbound traffic. It is stateful (return traffic is automatically allowed), operates at the instance level, and only supports allow rules (implicit deny).

---

## 7. Amazon Machine Image (AMI)

### 📖 Hindi Explanation:
AMI ek template hai jisme operating system, application server, aur applications pre-installed hoti hain. Jaise aap ek phone ka backup lete ho aur naye phone mein restore karte ho — waise hi AMI se aap naye EC2 instances launch kar sakte ho same configuration ke saath.

**Types:**
- **AWS Provided AMIs:** Amazon Linux, Ubuntu, Windows Server
- **Marketplace AMIs:** Third-party software pre-installed
- **Custom AMIs:** Aap khud banaate ho apni configurations ke saath

### 🎯 Interview Q&A:

**Q: What is an AMI?**  
**A:** An AMI (Amazon Machine Image) is a pre-configured template that contains the software configuration (OS, application server, applications) required to launch an EC2 instance. AMIs can be shared, copied across regions, and used to launch multiple identical instances.

**Q: Can you copy an AMI to another region?**  
**A:** Yes, AMIs are region-specific but can be copied to other regions using the `CopyImage` API or console. This is useful for disaster recovery and multi-region deployments.

---

## 8. Auto Scaling

### 📖 Hindi Explanation:
Auto Scaling automatically aapke EC2 instances ki sankhya badhata ya ghatata hai demand ke according. Jaise ek restaurant mein zyada customers aaye toh zyada waiters hire karo, kam aaye toh kam karo.

**Components:**
- **Launch Configuration/Template:** Kaunsi instance launch karni hai (AMI, instance type, etc.)
- **Auto Scaling Group (ASG):** Minimum, maximum, aur desired capacity define karo
- **Scaling Policies:** Kab scale karna hai (CPU > 80% toh scale out, < 20% toh scale in)

**Scaling Types:**
- **Dynamic Scaling:** CloudWatch alarms ke basis pe
- **Predictive Scaling:** ML-based future demand predict karta hai
- **Scheduled Scaling:** Fixed time pe scale karo (jaise raat ko kam karo)

### 🎯 Interview Q&A:

**Q: What is Auto Scaling in AWS?**  
**A:** AWS Auto Scaling automatically adjusts the number of EC2 instances in a group based on demand or scheduled actions. It ensures you have the right number of instances to handle the application load, improving availability and reducing costs.

**Q: What is the difference between scaling out and scaling up?**  
**A:**
- **Scaling Out (Horizontal)** – Adding more instances to distribute load
- **Scaling Up (Vertical)** – Increasing the size/capacity of an existing instance

**Q: What are the components of Auto Scaling?**  
**A:**
1. **Launch Template** – Defines instance configuration
2. **Auto Scaling Group** – Defines min, max, desired capacity
3. **Scaling Policy** – Defines when to add/remove instances (target tracking, step scaling, simple scaling)

---

## 9. Elastic Load Balancer (ELB)

### 📖 Hindi Explanation:
Load Balancer traffic ko multiple EC2 instances mein distribute karta hai. Jaise ek mall mein multiple billing counters hote hain taaki ek pe zyada load na aaye.

**Types:**
- **Application Load Balancer (ALB):** Layer 7 (HTTP/HTTPS) — URL-based routing
- **Network Load Balancer (NLB):** Layer 4 (TCP/UDP) — Ultra-low latency, millions of requests handle karta hai
- **Gateway Load Balancer (GWLB):** Layer 3 — Third-party virtual appliances ke liye
- **Classic Load Balancer (CLB):** Purana hai, ab recommend nahi hota

### 🎯 Interview Q&A:

**Q: What is Elastic Load Balancing?**  
**A:** ELB automatically distributes incoming application traffic across multiple targets (EC2 instances, containers, IPs) in one or more Availability Zones. It improves fault tolerance and availability.

**Q: What is the difference between ALB and NLB?**  
**A:**
- **ALB** operates at Layer 7 (HTTP/HTTPS), supports path-based/host-based routing, WebSockets, and is ideal for microservices.
- **NLB** operates at Layer 4 (TCP/UDP/TLS), handles millions of requests per second with ultra-low latency, supports static IPs, and is ideal for real-time applications.

---

## 10. AWS Lambda

### 📖 Hindi Explanation:
Lambda ek serverless compute service hai — matlab aapko koi server manage nahi karna. Bas code likho, upload karo, aur jab trigger hoga tab chalega. Aap sirf execution time ke liye pay karte ho.

Jaise ek light sensor — jab koi aaye toh light on ho, koi na ho toh off rahe. Aap bulb ka bill tabhi pay karo jab wo on ho.

**Key Features:**
- Supports: Python, Node.js, Java, Go, C#, Ruby
- Maximum execution time: 15 minutes
- Memory: 128 MB to 10 GB
- Triggers: S3, API Gateway, DynamoDB, SQS, CloudWatch Events, etc.

**Use Cases:**
- Image/video processing
- Real-time file processing
- API backends
- Scheduled tasks (cron jobs)

### 🎯 Interview Q&A:

**Q: What is AWS Lambda?**  
**A:** AWS Lambda is a serverless compute service that runs your code in response to events without provisioning or managing servers. You pay only for the compute time consumed (per millisecond). It automatically scales from zero to thousands of concurrent executions.

**Q: What are common triggers for Lambda?**  
**A:** Common triggers include: API Gateway (HTTP requests), S3 (file uploads), DynamoDB Streams, SQS messages, CloudWatch Events/EventBridge, SNS notifications, Kinesis streams, and Cognito events.

**Q: What are the limitations of Lambda?**  
**A:**
- Maximum execution timeout: 15 minutes
- Maximum deployment package: 50 MB (zipped), 250 MB (unzipped)
- Memory: 128 MB to 10,240 MB
- Concurrent executions: 1000 (default, can be increased)
- No persistent storage (use /tmp for 512 MB – 10 GB temporary storage)

---

## 11. Amazon VPC (Virtual Private Cloud)

### 📖 Hindi Explanation:
VPC aapka apna private network hai AWS cloud mein. Jaise aapka apna ghar hai jisme aap decide karte ho ki kaun andar aa sakta hai, kaun nahi.

**Key Components:**
- **Subnet:** VPC ka ek portion. Public subnet (internet accessible) aur Private subnet (isolated).
- **Internet Gateway (IGW):** VPC ko internet se connect karta hai.
- **NAT Gateway:** Private subnet ke instances ko internet access deta hai bina unhe expose kiye.
- **Route Table:** Traffic kahan jaayega yeh decide karta hai.
- **NACL (Network ACL):** Subnet level firewall (stateless).
- **Security Group:** Instance level firewall (stateful).

**Example Architecture:**
```
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24) → Internet Gateway
│   └── Web Server (EC2)
├── Private Subnet (10.0.2.0/24) → NAT Gateway
│   └── Application Server (EC2)
└── Private Subnet (10.0.3.0/24)
    └── Database (RDS)
```

### 🎯 Interview Q&A:

**Q: What is Amazon VPC?**  
**A:** Amazon VPC (Virtual Private Cloud) lets you provision a logically isolated section of AWS Cloud where you launch AWS resources in a virtual network that you define. You have complete control over IP addressing, subnets, route tables, and network gateways.

**Q: What is the difference between Security Group and NACL?**  
**A:**
| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance level | Subnet level |
| State | Stateful | Stateless |
| Rules | Allow only | Allow + Deny |
| Evaluation | All rules evaluated | Rules processed in order |
| Default | Denies all inbound | Allows all traffic |

**Q: What is the difference between Internet Gateway and NAT Gateway?**  
**A:**
- **Internet Gateway** – Enables instances in public subnets to communicate with the internet (bidirectional).
- **NAT Gateway** – Allows instances in private subnets to initiate outbound internet connections while preventing inbound access from the internet.

---

## 12. Amazon Route 53

### 📖 Hindi Explanation:
Route 53 AWS ka DNS (Domain Name System) service hai. Jaise phone mein contact list hoti hai — naam dalo, number mil jaata hai — waise hi Route 53 domain name (google.com) ko IP address mein convert karta hai.

**Features:**
- Domain Registration (domain khareedna)
- DNS Routing (traffic direct karna)
- Health Checks (server health monitor karna)

**Routing Policies:**
- **Simple:** Ek IP return karo
- **Weighted:** Traffic ko percentage mein divide karo
- **Latency-based:** Sabse kam latency wale region pe bhejo
- **Failover:** Primary fail ho toh secondary pe bhejo
- **Geolocation:** User ki location ke basis pe route karo
- **Multi-value:** Multiple healthy IPs return karo

### 🎯 Interview Q&A:

**Q: What is Amazon Route 53?**  
**A:** Amazon Route 53 is a highly available and scalable DNS web service. It performs three main functions: domain registration, DNS routing, and health checking. It provides 100% availability SLA.

**Q: What routing policies does Route 53 support?**  
**A:** Route 53 supports: Simple, Weighted, Latency-based, Failover, Geolocation, Geoproximity, and Multi-value answer routing policies. Each serves different use cases like A/B testing (weighted), disaster recovery (failover), and performance optimization (latency-based).

---

## 13. AWS Direct Connect

### 📖 Hindi Explanation:
Direct Connect aapke on-premises data center ko AWS se directly connect karta hai — bina internet use kiye. Jaise aapke ghar se office tak ek private road ban jaaye — no traffic, no delay.

Iska use tab hota hai jab aapko consistent network performance chahiye, high bandwidth chahiye, ya compliance requirements hain ki data public internet pe na jaaye.

### 🎯 Interview Q&A:

**Q: What is AWS Direct Connect?**  
**A:** AWS Direct Connect is a dedicated network connection from your premises to AWS. It provides more consistent network performance, reduced bandwidth costs, and a more reliable connection than internet-based connections. It supports 1 Gbps and 10 Gbps dedicated connections.

**Q: When would you use Direct Connect over VPN?**  
**A:** Use Direct Connect when you need consistent, low-latency connectivity, large data transfers, or regulatory compliance requiring private connections. Use VPN for quick setup, lower cost, or as a backup for Direct Connect.

---

## 14. Amazon S3 (Simple Storage Service)

### 📖 Hindi Explanation:
S3 AWS ka object storage service hai — jahan aap koi bhi file (photos, videos, backups, logs) store kar sakte ho. Yeh practically unlimited storage deta hai. Jaise Google Drive hai lekin developers ke liye.

**Key Concepts:**
- **Bucket:** Container jisme files store hoti hain (globally unique name)
- **Object:** File + metadata (max 5 TB per object)
- **Key:** Object ka unique identifier (file path)

**Storage Classes:**
| Class | Use Case | Cost |
|-------|----------|------|
| S3 Standard | Frequently accessed data | Highest |
| S3 Intelligent-Tiering | Unknown access patterns | Auto-moves |
| S3 Standard-IA | Infrequent access, quick retrieval | Lower |
| S3 One Zone-IA | Infrequent, non-critical | Even lower |
| S3 Glacier Instant | Archive, instant access | Low |
| S3 Glacier Flexible | Archive, mins to hours | Very low |
| S3 Glacier Deep Archive | Long-term archive, 12 hours | Lowest |

**Important Features:**
- Versioning (purani files bhi rakho)
- Lifecycle Policies (automatically class change karo)
- Encryption (SSE-S3, SSE-KMS, SSE-C)
- Bucket Policies & ACLs
- Cross-Region Replication (CRR)
- Static Website Hosting
- Pre-signed URLs (temporary access)

### 🎯 Interview Q&A:

**Q: What is Amazon S3?**  
**A:** Amazon S3 is an object storage service offering industry-leading scalability, availability (99.99%), durability (11 9's), security, and performance. It stores unlimited data as objects within buckets and is commonly used for backup, archival, data lakes, static website hosting, and application data.

**Q: What is the difference between S3 storage classes?**  
**A:** S3 offers multiple storage classes optimized for different use cases:
- **Standard** – Frequently accessed, low latency
- **Intelligent-Tiering** – Automatically moves data between tiers
- **Standard-IA** – Infrequent access, rapid retrieval needed
- **Glacier** – Archive data with retrieval in minutes to hours
- **Glacier Deep Archive** – Cheapest, retrieval in 12 hours

**Q: How do you secure data in S3?**  
**A:**
1. Bucket policies and IAM policies for access control
2. Block Public Access settings
3. Server-side encryption (SSE-S3, SSE-KMS, SSE-C)
4. Client-side encryption
5. VPC Endpoints for private access
6. Access logging and CloudTrail
7. Object Lock for WORM compliance
8. Versioning to protect against accidental deletion

---

## 15. Amazon EBS (Elastic Block Store)

### 📖 Hindi Explanation:
EBS ek block storage hai jo EC2 instances ke saath attach hota hai — jaise aapke computer ki hard disk. EC2 band bhi ho jaaye toh data safe rehta hai (persistent storage).

**Volume Types:**
- **gp3/gp2 (General Purpose SSD):** General workloads, boot volumes
- **io2/io1 (Provisioned IOPS SSD):** Databases jo high performance chahte hain
- **st1 (Throughput Optimized HDD):** Big data, data warehousing
- **sc1 (Cold HDD):** Infrequent access, lowest cost

**Key Features:**
- Snapshots (backup to S3)
- Encryption
- Multi-attach (io1/io2 only)
- Ek AZ mein bound hota hai (cross-AZ ke liye snapshot use karo)

### 🎯 Interview Q&A:

**Q: What is Amazon EBS?**  
**A:** Amazon EBS provides persistent block-level storage volumes for EC2 instances. EBS volumes persist independently from the running instance, are replicated within their AZ, and support snapshots to S3 for backup and replication.

**Q: What is the difference between EBS and Instance Store?**  
**A:**
- **EBS** – Persistent, network-attached, survives instance stop/termination, supports snapshots
- **Instance Store** – Ephemeral, physically attached to host, data lost on stop/termination, higher I/O performance

---

## 16. Amazon EFS (Elastic File System)

### 📖 Hindi Explanation:
EFS ek shared file storage hai jo multiple EC2 instances simultaneously access kar sakti hain. Jaise office mein ek shared drive hoti hai jise sab log use karte hain. Yeh NFS protocol use karta hai.

EBS vs EFS: EBS ek instance ke saath attach hota hai (ek hard disk), EFS multiple instances ke saath share hota hai (ek network drive).

### 🎯 Interview Q&A:

**Q: What is Amazon EFS?**  
**A:** Amazon EFS is a fully managed, elastic, shared file system that uses NFS protocol. It automatically grows and shrinks as you add/remove files, can be mounted concurrently by thousands of EC2 instances across multiple AZs, and supports both Linux workloads.

**Q: EBS vs EFS vs S3?**  
**A:**
- **EBS** – Block storage, single AZ, single instance attachment, lowest latency
- **EFS** – File storage, multi-AZ, shared across instances, NFS protocol
- **S3** – Object storage, global, unlimited, accessed via API/HTTP

---

## 17. AWS Storage Gateway

### 📖 Hindi Explanation:
Storage Gateway aapke on-premises environment ko AWS cloud storage se connect karta hai. Jaise ek bridge jo aapke local data center aur cloud ke beech mein hai.

**Types:**
- **File Gateway:** S3 ko NFS/SMB mount point ke roop mein access karo
- **Volume Gateway:** iSCSI block storage jo S3 pe backed hai
- **Tape Gateway:** Virtual tape library jo Glacier pe archive hota hai

### 🎯 Interview Q&A:

**Q: What is AWS Storage Gateway?**  
**A:** AWS Storage Gateway is a hybrid cloud storage service that provides on-premises access to virtually unlimited cloud storage. It seamlessly connects on-premises environments to AWS storage services (S3, S3 Glacier) using standard protocols like NFS, SMB, and iSCSI.

---

## 18. Amazon CloudFront

### 📖 Hindi Explanation:
CloudFront ek CDN (Content Delivery Network) hai. Yeh aapki website/app ka content (images, videos, APIs) duniya bhar ke edge locations pe cache karta hai taaki users ko fast access mile.

Jaise aap Delhi mein ho aur US server se data maang rahe ho — slow hoga. But agar wo data Delhi ke paas ek edge location pe cached hai toh bahut fast milega.

**Features:**
- 400+ Edge Locations globally
- HTTPS support (free SSL)
- Origin Access Identity (S3 content secure access)
- Lambda@Edge (code at edge locations)
- Real-time logs
- DDoS protection (AWS Shield integration)

### 🎯 Interview Q&A:

**Q: What is Amazon CloudFront?**  
**A:** Amazon CloudFront is a fast CDN service that securely delivers data, videos, applications, and APIs to users globally with low latency and high transfer speeds. It caches content at 400+ edge locations worldwide and integrates with AWS Shield for DDoS mitigation.

**Q: What is Origin Access Identity (OAI)?**  
**A:** OAI is a special CloudFront identity that you associate with your distribution to restrict direct access to your S3 bucket. Users can only access S3 content through CloudFront, not directly via S3 URLs, ensuring your content is served through the CDN.

---

## 19. Amazon RDS (Relational Database Service)

### 📖 Hindi Explanation:
RDS ek managed relational database service hai. AWS aapke liye hardware provisioning, database setup, patching, aur backups handle karta hai. Aap sirf apne data pe focus karo.

**Supported Engines:**
- MySQL
- PostgreSQL
- MariaDB
- Oracle
- SQL Server
- Amazon Aurora (AWS ka custom engine — 5x faster than MySQL)

**Key Features:**
- Multi-AZ Deployment (high availability — automatic failover)
- Read Replicas (read performance improve karo — 5 replicas tak)
- Automated Backups (point-in-time recovery)
- Encryption at rest and in transit
- Auto Scaling storage

**Aurora Special:**
- 5x faster than MySQL, 3x faster than PostgreSQL
- Auto-scales up to 128 TB
- 6 copies of data across 3 AZs
- Aurora Serverless available

### 🎯 Interview Q&A:

**Q: What is Amazon RDS?**  
**A:** Amazon RDS is a managed relational database service supporting six engines (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora). It handles provisioning, patching, backup, recovery, and scaling, allowing you to focus on application logic.

**Q: What is the difference between Multi-AZ and Read Replicas?**  
**A:**
- **Multi-AZ** – Synchronous replication to standby in another AZ for high availability. Automatic failover. Not used for read scaling.
- **Read Replicas** – Asynchronous replication for read scaling. Can be cross-region. Can be promoted to standalone DB. Up to 15 (Aurora) or 5 (others).

**Q: What is Amazon Aurora?**  
**A:** Aurora is a MySQL/PostgreSQL-compatible relational database built for the cloud. It's 5x faster than MySQL, stores 6 copies of data across 3 AZs, auto-scales to 128 TB, supports up to 15 read replicas, and offers a serverless option.

---

## 20. Amazon DynamoDB

### 📖 Hindi Explanation:
DynamoDB ek fully managed NoSQL database hai — matlab non-relational database. Yeh key-value aur document data model support karta hai. Bahut fast hai (single-digit millisecond latency) aur automatically scale hota hai.

Jaise ek dictionary hai — key (word) dalo, value (meaning) mil jaati hai. Tables mein Primary Key hoti hai (Partition Key + optional Sort Key).

**Key Features:**
- Serverless (no server manage karna)
- Auto scaling
- DAX (DynamoDB Accelerator) — in-memory caching
- Global Tables (multi-region replication)
- Streams (change data capture)
- TTL (automatic item expiry)
- On-demand ya Provisioned capacity mode

### 🎯 Interview Q&A:

**Q: What is DynamoDB?**  
**A:** DynamoDB is a fully managed, serverless NoSQL database that delivers single-digit millisecond performance at any scale. It supports key-value and document data models, offers built-in security, backup/restore, in-memory caching (DAX), and multi-region replication (Global Tables).

**Q: When would you use DynamoDB vs RDS?**  
**A:**
- **DynamoDB** – High-scale, low-latency, flexible schema, key-value access patterns, serverless
- **RDS** – Complex queries, joins, transactions, fixed schema, SQL-based applications

**Q: What is DynamoDB DAX?**  
**A:** DAX (DynamoDB Accelerator) is a fully managed, in-memory cache for DynamoDB that delivers up to 10x performance improvement (microsecond latency). It's compatible with existing DynamoDB API calls, requiring no application logic changes.

---

## 21. Amazon Redshift

### 📖 Hindi Explanation:
Redshift ek data warehouse service hai jo petabytes of data analyze karne ke liye hai. Jaise aapko crores of rows mein se insights nikalni hain — Redshift yeh kaam bahut fast karta hai.

Yeh OLAP (Online Analytical Processing) ke liye hai, OLTP (Online Transaction Processing) ke liye nahi. Business intelligence tools (Tableau, QuickSight) ke saath integrate hota hai.

**Features:**
- Columnar storage (fast analytical queries)
- Massively Parallel Processing (MPP)
- Redshift Spectrum (query data directly in S3)
- Auto-scaling
- ML-based query optimization

### 🎯 Interview Q&A:

**Q: What is Amazon Redshift?**  
**A:** Amazon Redshift is a fully managed, petabyte-scale data warehouse service. It uses columnar storage, massively parallel processing (MPP), and machine learning to deliver fast query performance on structured and semi-structured data for analytics and BI workloads.

**Q: What is the difference between RDS and Redshift?**  
**A:**
- **RDS** – OLTP (transactional), row-based, optimized for INSERT/UPDATE operations
- **Redshift** – OLAP (analytical), column-based, optimized for complex aggregate queries over large datasets

---

## 22. Amazon ElastiCache

### 📖 Hindi Explanation:
ElastiCache ek in-memory caching service hai. Yeh frequently accessed data ko memory mein store karta hai taaki database pe baar baar query na karni pade. Bahut fast response (microseconds).

**Two Engines:**
- **Redis:** Advanced features — persistence, replication, pub/sub, sorted sets
- **Memcached:** Simple caching, multi-threaded

Jaise restaurant mein bar counter pe ready drinks rakhte hain taaki baar baar kitchen mein na jaana pade.

### 🎯 Interview Q&A:

**Q: What is Amazon ElastiCache?**  
**A:** ElastiCache is a fully managed in-memory data store and cache service supporting Redis and Memcached. It improves application performance by caching frequently accessed data, reducing database load, and providing sub-millisecond latency.

**Q: Redis vs Memcached in ElastiCache?**  
**A:**
- **Redis** – Persistence, replication, Multi-AZ, pub/sub, complex data types, backup/restore
- **Memcached** – Simple key-value, multi-threaded, no persistence, horizontal scaling via sharding

---

## 23. Amazon Kinesis

### 📖 Hindi Explanation:
Kinesis real-time streaming data collect, process, aur analyze karne ke liye hai. Jaise live cricket score — data continuously aa raha hai aur real-time process ho raha hai.

**Components:**
- **Kinesis Data Streams:** Custom real-time processing
- **Kinesis Data Firehose:** Data ko S3, Redshift, Elasticsearch mein deliver karo (easiest way)
- **Kinesis Data Analytics:** SQL/Flink se real-time analytics
- **Kinesis Video Streams:** Video streams process karo

### 🎯 Interview Q&A:

**Q: What is Amazon Kinesis?**  
**A:** Amazon Kinesis is a platform for streaming data on AWS. It enables real-time collection, processing, and analysis of streaming data such as IoT telemetry, application logs, clickstreams, and social media feeds at any scale.

**Q: What is the difference between Kinesis Data Streams and Firehose?**  
**A:**
- **Data Streams** – Custom processing with consumers, manual scaling (shards), 24-hour to 365-day retention
- **Firehose** – Fully managed delivery to destinations (S3, Redshift, OpenSearch), auto-scaling, near real-time (60-second buffer)

---

## 24. Amazon EMR (Elastic MapReduce)

### 📖 Hindi Explanation:
EMR ek managed big data platform hai jo Apache Hadoop, Spark, Hive, Presto jaise frameworks run karta hai. Petabytes of data process karna ho toh EMR use karo.

Jaise agar aapko 100 TB data analyze karna hai toh EMR automatically cluster create karega, process karega, aur terminate kar dega — aap sirf processing ka pay karo.

### 🎯 Interview Q&A:

**Q: What is Amazon EMR?**  
**A:** Amazon EMR is a managed cluster platform for running big data frameworks like Apache Hadoop, Spark, HBase, Presto, and Flink. It processes vast amounts of data quickly and cost-effectively, auto-scaling clusters as needed.

---

## 25. AWS Data Pipeline

### 📖 Hindi Explanation:
Data Pipeline ek orchestration service hai jo data ko move aur transform karne ke liye use hoti hai — different AWS services aur on-premises sources ke beech. Jaise ek conveyor belt jo data ko ek jagah se doosri jagah le jaati hai scheduled basis pe.

### 🎯 Interview Q&A:

**Q: What is AWS Data Pipeline?**  
**A:** AWS Data Pipeline is a web service for processing and moving data between different AWS compute and storage services, and on-premises data sources at specified intervals. It handles scheduling, retry logic, and failure notifications.

---

## 26. Amazon Machine Learning

### 📖 Hindi Explanation:
AWS pe Machine Learning ke liye kaafi services hain:

- **Amazon SageMaker:** ML models build, train, deploy karo (full ML lifecycle)
- **Amazon Rekognition:** Image/video analysis (face detection, object recognition)
- **Amazon Comprehend:** NLP (text analysis, sentiment detection)
- **Amazon Polly:** Text to Speech
- **Amazon Lex:** Chatbots banao (Alexa jaisa)
- **Amazon Translate:** Language translation
- **Amazon Transcribe:** Speech to Text
- **Amazon Forecast:** Time series forecasting
- **Amazon Bedrock:** Generative AI (foundation models)

### 🎯 Interview Q&A:

**Q: What ML services does AWS offer?**  
**A:** AWS offers ML services at three layers:
1. **AI Services** (no ML expertise needed) – Rekognition, Comprehend, Polly, Lex, Translate, Transcribe
2. **ML Platform** – SageMaker for building, training, deploying custom models
3. **ML Frameworks** – Support for TensorFlow, PyTorch, MXNet on EC2/SageMaker
4. **Generative AI** – Amazon Bedrock for foundation models (Claude, Titan, etc.)

---

## 27. Amazon SQS (Simple Queue Service)

### 📖 Hindi Explanation:
SQS ek message queue service hai. Jaise post office mein letters queue mein rehte hain — ek service message bhejti hai, doosri service baad mein process karti hai. Yeh services ko decouple karta hai.

**Types:**
- **Standard Queue:** Unlimited throughput, at-least-once delivery, best-effort ordering
- **FIFO Queue:** Exactly-once processing, strict ordering, 300 msg/sec (3000 with batching)

**Key Features:**
- Message retention: 1 min to 14 days (default 4 days)
- Message size: max 256 KB
- Visibility timeout (processing ke time message hide hota hai)
- Dead Letter Queue (failed messages ke liye)
- Long polling (reduce empty responses)

### 🎯 Interview Q&A:

**Q: What is Amazon SQS?**  
**A:** Amazon SQS is a fully managed message queuing service that decouples and scales microservices, distributed systems, and serverless applications. It eliminates the complexity of managing message-oriented middleware.

**Q: What is the difference between SQS and SNS?**  
**A:**
- **SQS** – Pull-based, message queue, one consumer processes each message, decoupling
- **SNS** – Push-based, pub/sub, one message delivered to multiple subscribers simultaneously

---

## 28. Amazon SNS (Simple Notification Service)

### 📖 Hindi Explanation:
SNS ek pub/sub (publish/subscribe) messaging service hai. Ek message publish karo aur woh saare subscribers ko turant mil jaata hai. Jaise WhatsApp group mein ek message sab ko milta hai.

**Subscribers:**
- Email, SMS, HTTP/HTTPS endpoints
- SQS queues, Lambda functions
- Mobile push notifications

### 🎯 Interview Q&A:

**Q: What is Amazon SNS?**  
**A:** Amazon SNS is a fully managed pub/sub messaging service for A2A (application-to-application) and A2P (application-to-person) communication. A publisher sends a message to an SNS topic, which fans out to all subscribed endpoints (Lambda, SQS, HTTP, email, SMS).

---

## 29. Amazon SES (Simple Email Service)

### 📖 Hindi Explanation:
SES ek email sending service hai. Bulk emails (marketing, notifications, transactional emails) bhejne ke liye use hota hai. Bahut cost-effective hai — $0.10 per 1000 emails.

### 🎯 Interview Q&A:

**Q: What is Amazon SES?**  
**A:** Amazon SES is a cost-effective, scalable email service for sending transactional, marketing, and bulk emails. It supports SMTP and API-based sending, email receiving, and provides deliverability metrics.

---

## 30. Amazon SWF (Simple Workflow Service)

### 📖 Hindi Explanation:
SWF distributed applications ke tasks ko coordinate karta hai. Jaise ek order processing system — payment verify karo, inventory check karo, ship karo — sab steps ko manage karta hai.

Note: Ab zyada tar use cases ke liye AWS Step Functions recommend hota hai.

### 🎯 Interview Q&A:

**Q: What is Amazon SWF?**  
**A:** Amazon SWF helps coordinate work across distributed application components. It manages task execution, tracks state, and handles failures. However, AWS Step Functions is now the recommended service for most new workflow orchestration use cases.

---

## 31. Amazon WorkMail

### 📖 Hindi Explanation:
WorkMail ek managed email aur calendar service hai business ke liye — jaise Microsoft Outlook ya Google Workspace ka alternative. Secure hai aur existing email clients ke saath kaam karta hai.

### 🎯 Interview Q&A:

**Q: What is Amazon WorkMail?**  
**A:** Amazon WorkMail is a secure, managed business email and calendar service compatible with existing desktop and mobile email clients (Outlook, iOS/Android mail). It integrates with existing corporate directories.

---

## 32. Amazon WorkSpaces

### 📖 Hindi Explanation:
WorkSpaces ek managed virtual desktop (DaaS - Desktop as a Service) hai. Aap cloud mein Windows ya Linux desktop access kar sakte ho kisi bhi device se. Jaise remote desktop but fully managed by AWS.

### 🎯 Interview Q&A:

**Q: What is Amazon WorkSpaces?**  
**A:** Amazon WorkSpaces is a managed Desktop-as-a-Service (DaaS) solution. It provisions cloud-based Windows or Linux desktops accessible from any supported device, eliminating the need to manage physical desktops or VDI infrastructure.

---

## 33. AWS CloudFormation

### 📖 Hindi Explanation:
CloudFormation Infrastructure as Code (IaC) service hai. Aap ek template (JSON/YAML) likhte ho jisme saari AWS resources define hoti hain, aur CloudFormation automatically sab create kar deta hai.

Jaise ek blueprint se ghar ban jaata hai — waise hi template se poora infrastructure ban jaata hai. Isko "Stack" kehte hain.

**Benefits:**
- Repeatable deployments
- Version control mein rakho
- Rollback on failure
- Cost estimation
- Drift detection

```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0abcdef1234567890
      InstanceType: t2.micro
      SecurityGroups:
        - !Ref MySecurityGroup
```

### 🎯 Interview Q&A:

**Q: What is AWS CloudFormation?**  
**A:** AWS CloudFormation is an IaC service that lets you model, provision, and manage AWS resources using templates (JSON/YAML). It creates resources in a predictable, repeatable manner, supports rollback on failures, and enables version-controlled infrastructure.

**Q: What is a CloudFormation Stack?**  
**A:** A Stack is a collection of AWS resources that are created, updated, or deleted together as a single unit from a CloudFormation template. Stacks can be nested and can reference outputs from other stacks.

---

## 34. AWS CloudWatch

### 📖 Hindi Explanation:
CloudWatch AWS ka monitoring service hai. Yeh aapke resources ki metrics collect karta hai, logs store karta hai, aur alarms set karta hai.

**Components:**
- **Metrics:** CPU usage, network in/out, disk I/O, etc.
- **Alarms:** Threshold cross hone pe notification bhejo ya action lo
- **Logs:** Application/system logs collect aur analyze karo
- **Dashboards:** Visual monitoring
- **Events/EventBridge:** AWS resource changes pe react karo

Jaise car ka dashboard — speed, fuel, temperature sab dikhata hai aur warning light on hoti hai agar kuch galat ho.

### 🎯 Interview Q&A:

**Q: What is Amazon CloudWatch?**  
**A:** Amazon CloudWatch is a monitoring and observability service that collects metrics, logs, and events from AWS resources and applications. It provides dashboards, alarms, anomaly detection, and automated actions to maintain operational health.

**Q: What is the difference between CloudWatch and CloudTrail?**  
**A:**
- **CloudWatch** – Monitors performance metrics and operational health (CPU, memory, custom metrics)
- **CloudTrail** – Records API calls and user activity for auditing and compliance (who did what, when)

---

## 35. AWS CloudTrail

### 📖 Hindi Explanation:
CloudTrail aapke AWS account mein hone wali har API call ko record karta hai. Kaun ne kya kiya, kab kiya, kahan se kiya — sab log hota hai. Yeh audit aur security ke liye essential hai.

Jaise CCTV camera jo sab record karta hai — baad mein dekh sakte ho ki kya hua tha.

### 🎯 Interview Q&A:

**Q: What is AWS CloudTrail?**  
**A:** AWS CloudTrail is a governance, compliance, and audit service that records all API calls made in your AWS account. It logs who made the call, when, from where (source IP), and what was the request/response. Logs can be stored in S3 and analyzed with Athena.

---

## 36. AWS Trusted Advisor

### 📖 Hindi Explanation:
Trusted Advisor aapke AWS account ko scan karke recommendations deta hai 5 categories mein:
1. **Cost Optimization:** Paisa kahan bach sakta hai
2. **Performance:** Kahan improve kar sakte ho
3. **Security:** Kya vulnerabilities hain
4. **Fault Tolerance:** Kya high availability ensure hai
5. **Service Limits:** Kya limits paas aa rahe hain

Free tier mein limited checks milte hain. Business/Enterprise support plan mein saare checks milte hain.

### 🎯 Interview Q&A:

**Q: What is AWS Trusted Advisor?**  
**A:** AWS Trusted Advisor is an online tool that inspects your AWS environment and provides real-time recommendations for cost optimization, performance, security, fault tolerance, and service limits. It acts as a customized cloud expert.

---

## 37. AWS KMS & Security

### 📖 Hindi Explanation:
AWS Security ek shared responsibility model pe kaam karta hai:
- **AWS responsible:** Physical security, hardware, network infrastructure
- **Customer responsible:** Data encryption, IAM, OS patching, firewall rules

**Key Security Services:**
- **KMS (Key Management Service):** Encryption keys create aur manage karo
- **AWS Shield:** DDoS protection (Standard = free, Advanced = paid)
- **AWS WAF:** Web Application Firewall — SQL injection, XSS block karo
- **AWS GuardDuty:** Threat detection — malicious activity detect karo
- **AWS Inspector:** EC2 instances ki vulnerabilities scan karo
- **AWS Macie:** S3 mein sensitive data (PII) detect karo
- **AWS Secrets Manager:** Passwords, API keys securely store karo
- **AWS Certificate Manager:** Free SSL/TLS certificates

### 🎯 Interview Q&A:

**Q: What is the Shared Responsibility Model?**  
**A:** In AWS, security is shared:
- **AWS** is responsible for security **of** the cloud (physical infrastructure, hypervisor, managed services)
- **Customer** is responsible for security **in** the cloud (data, IAM, OS patching, encryption, network configuration)

**Q: What is AWS KMS?**  
**A:** AWS KMS (Key Management Service) is a managed service for creating and controlling encryption keys used to encrypt data. It integrates with most AWS services, supports key rotation, and provides audit trails via CloudTrail.

**Q: How do you protect against DDoS attacks in AWS?**  
**A:**
1. AWS Shield Standard (automatic, free)
2. AWS Shield Advanced (paid, 24/7 DRT support)
3. AWS WAF (custom rules)
4. CloudFront (absorb attacks at edge)
5. Route 53 (DNS-level protection)
6. Auto Scaling (absorb traffic spikes)

---

## 38. Amazon API Gateway

### 📖 Hindi Explanation:
API Gateway ek fully managed service hai jo APIs create, publish, maintain, aur secure karne ke liye use hoti hai. Yeh aapke backend (Lambda, EC2, any HTTP endpoint) ke liye ek "front door" hai.

Jaise ek restaurant ka waiter — customer (client) ka order leta hai aur kitchen (backend) tak pahunchata hai.

**Features:**
- REST APIs aur WebSocket APIs support
- Rate limiting & throttling
- API keys & usage plans
- Request/response transformation
- Caching
- Custom domain names
- Authorization (IAM, Cognito, Lambda authorizer)

### 🎯 Interview Q&A:

**Q: What is Amazon API Gateway?**  
**A:** Amazon API Gateway is a fully managed service for creating, publishing, maintaining, and securing REST, HTTP, and WebSocket APIs at any scale. It handles traffic management, authorization, monitoring, throttling, and API versioning.

**Q: How does API Gateway integrate with Lambda?**  
**A:** API Gateway acts as the HTTP endpoint that triggers Lambda functions. When a request hits the API, Gateway routes it to the appropriate Lambda function, passes the event payload, and returns the Lambda response to the client. This forms the basis of serverless architectures.

---

## 39. AWS Elastic Beanstalk

### 📖 Hindi Explanation:
Elastic Beanstalk ek PaaS service hai — aap sirf apna code upload karo, baaki sab (server provisioning, load balancing, auto scaling, monitoring) AWS handle karega.

Jaise Heroku jaisa — code do, deployment ho jaayegi. But aap chahein toh underlying resources ko bhi control kar sakte ho.

**Supported Platforms:** Java, .NET, PHP, Node.js, Python, Ruby, Go, Docker

### 🎯 Interview Q&A:

**Q: What is AWS Elastic Beanstalk?**  
**A:** Elastic Beanstalk is a PaaS that automatically handles deployment, capacity provisioning, load balancing, auto-scaling, and health monitoring. You upload your code, and it manages the infrastructure. You retain full control over the underlying resources if needed.

**Q: What is the difference between Elastic Beanstalk and CloudFormation?**  
**A:**
- **Elastic Beanstalk** – Application-focused PaaS, handles deployment automatically, limited to web apps
- **CloudFormation** – Infrastructure-focused IaC, templates define any AWS resource, full flexibility

---

## 40. Amazon ECS & EKS

### 📖 Hindi Explanation:
Containers (Docker) run karne ke liye AWS ke services:

- **ECS (Elastic Container Service):** AWS ka apna container orchestration service
- **EKS (Elastic Kubernetes Service):** Managed Kubernetes cluster
- **Fargate:** Serverless containers — koi server manage nahi karna, bas container run karo
- **ECR (Elastic Container Registry):** Docker images store karo (Docker Hub jaisa)

**ECS vs EKS:**
- ECS: Simple hai, AWS-native, choti teams ke liye
- EKS: Kubernetes use karte ho toh EKS, badi teams/multi-cloud ke liye

### 🎯 Interview Q&A:

**Q: What is the difference between ECS and EKS?**  
**A:**
- **ECS** – AWS-proprietary container orchestration, simpler, tightly integrated with AWS services
- **EKS** – Managed Kubernetes, open-source, portable across clouds, larger ecosystem

**Q: What is AWS Fargate?**  
**A:** AWS Fargate is a serverless compute engine for containers (works with both ECS and EKS). You don't manage servers or clusters — just define your container's CPU/memory requirements, and Fargate handles provisioning and scaling.

---

## 41. AWS CodePipeline (CI/CD)

### 📖 Hindi Explanation:
AWS mein CI/CD ke liye yeh services hain:

- **CodeCommit:** Git repository (GitHub jaisa)
- **CodeBuild:** Code compile aur test karo
- **CodeDeploy:** EC2, Lambda, ECS pe deploy karo
- **CodePipeline:** In sab ko ek pipeline mein connect karo

Jaise ek assembly line — code commit hone pe automatically build hoga, test hoga, aur deploy ho jaayega.

### 🎯 Interview Q&A:

**Q: What is AWS CodePipeline?**  
**A:** AWS CodePipeline is a fully managed continuous delivery service that automates release pipelines for application and infrastructure updates. It orchestrates build, test, and deploy phases, integrating with CodeCommit, CodeBuild, CodeDeploy, and third-party tools like GitHub and Jenkins.

---

## 42. AWS Well-Architected Framework

### 📖 Hindi Explanation:
AWS Well-Architected Framework 6 pillars pe based hai — yeh best practices hain cloud architecture ke liye:

1. **Operational Excellence:** Operations automate karo, monitor karo, improve karo
2. **Security:** Data protect karo, access control, detect threats
3. **Reliability:** System recover hona chahiye failures se
4. **Performance Efficiency:** Resources efficiently use karo
5. **Cost Optimization:** Unnecessary expenses avoid karo
6. **Sustainability:** Environmental impact minimize karo

### 🎯 Interview Q&A:

**Q: What are the pillars of AWS Well-Architected Framework?**  
**A:** The six pillars are:
1. **Operational Excellence** – Automate operations, learn from failures
2. **Security** – Protect data, manage access, detect events
3. **Reliability** – Recover from failures, meet demand
4. **Performance Efficiency** – Use resources efficiently, adopt new tech
5. **Cost Optimization** – Avoid unnecessary costs, analyze spending
6. **Sustainability** – Minimize environmental impact of workloads

---

## 43. AWS Pricing & Billing

### 📖 Hindi Explanation:
AWS ka pricing model:
- **Pay-as-you-go:** Jitna use karo utna pay karo
- **Save when you reserve:** Long-term commitment pe discount
- **Pay less by using more:** Volume discounts

**Key Billing Services:**
- **AWS Free Tier:** 12 months free (750 hrs EC2, 5GB S3, etc.) + Always Free services
- **AWS Cost Explorer:** Spending analyze karo
- **AWS Budgets:** Budget set karo, alert paao
- **AWS Calculator:** Estimate karo kitna kharcha aayega
- **Consolidated Billing:** Multiple accounts ka ek bill (Organizations)

### 🎯 Interview Q&A:

**Q: What is the AWS Free Tier?**  
**A:** AWS Free Tier offers three types of free offers:
1. **12-Month Free** – Available for 12 months after signup (e.g., 750 hrs t2.micro EC2/month)
2. **Always Free** – Never expires (e.g., 1M Lambda requests/month, 25GB DynamoDB)
3. **Trials** – Short-term free trials for specific services

**Q: How does AWS pricing work?**  
**A:** AWS uses a pay-as-you-go model with no upfront costs. Key pricing principles:
- Compute: Pay per second/hour of usage
- Storage: Pay per GB stored per month
- Data Transfer: Inbound is free, outbound is charged per GB
- Savings: Reserved Instances (up to 75%), Spot Instances (up to 90%), volume discounts

---

## 🏆 Top 20 Most Asked AWS Interview Questions (Quick Fire)

| # | Question | Key Answer |
|---|----------|-----------|
| 1 | What is AWS? | Cloud platform with 200+ services, pay-as-you-go |
| 2 | What is EC2? | Virtual servers in the cloud |
| 3 | S3 vs EBS? | Object storage vs Block storage |
| 4 | What is VPC? | Your private network in AWS cloud |
| 5 | Security Group vs NACL? | Instance-level stateful vs Subnet-level stateless |
| 6 | What is IAM Role? | Temporary credentials for services/cross-account |
| 7 | What is Auto Scaling? | Automatically adjust instance count based on demand |
| 8 | ALB vs NLB? | Layer 7 HTTP routing vs Layer 4 TCP/UDP low latency |
| 9 | What is Lambda? | Serverless compute, event-driven, pay per execution |
| 10 | Multi-AZ vs Read Replica? | HA failover vs Read scaling |
| 11 | What is CloudFormation? | Infrastructure as Code using templates |
| 12 | Route 53 routing policies? | Simple, Weighted, Latency, Failover, Geo |
| 13 | Shared Responsibility Model? | AWS = of cloud, Customer = in cloud |
| 14 | SQS vs SNS? | Pull queue vs Push pub/sub |
| 15 | What is CloudFront? | CDN, content cached at edge locations |
| 16 | DynamoDB vs RDS? | NoSQL flexible vs SQL relational |
| 17 | What is Elastic Beanstalk? | PaaS, upload code, auto-deploys |
| 18 | ECS vs EKS? | AWS-native containers vs Managed Kubernetes |
| 19 | What is CloudWatch? | Monitoring metrics, logs, alarms |
| 20 | What is CloudTrail? | Audit log of all API calls |

---

## 📚 Additional Resources

- [AWS Official Documentation](https://docs.aws.amazon.com/)
- [AWS Free Tier](https://aws.amazon.com/free/)
- [TutorialsPoint AWS Tutorial](https://www.tutorialspoint.com/amazon_web_services/index.htm)
- [AWS Well-Architected Labs](https://www.wellarchitectedlabs.com/)
- [AWS Skill Builder (Free Training)](https://skillbuilder.aws/)

---

## 🎓 AWS Certifications Path

```
Cloud Practitioner (Foundational)
        ↓
Solutions Architect Associate → SysOps Administrator Associate → Developer Associate
        ↓
Solutions Architect Professional → DevOps Engineer Professional
        ↓
Specialty: Security | Database | ML | Networking | Data Analytics | SAP
```

---

> **⭐ Star this repo if you found it helpful!**  
> **📝 Contributions welcome — add more topics, fix errors, or improve explanations.**

---

*Created with ❤️ for AWS learners | Hindi + English | Zero to Advanced*
