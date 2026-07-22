---
title: "Week 3 Worklog"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---



### Week 3 Objectives:

* Eliminate hardcoded credentials by attaching IAM Roles to EC2 instances using Instance Metadata Service (IMDSv2).
* Master cloud-native development workflows using AWS Cloud9 IDE with pre-authenticated AWS environment access.
* Configure Amazon S3 for secure object storage, static website hosting, and granular access control via Bucket Policies.
* Provision and manage Amazon RDS relational databases within private subnets using DB Subnet Groups and Security Group chaining.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Studied IAM Roles for EC2 and Instance Profile mechanics <br> - Explored AWS Cloud9 cloud-based IDE setup and environment customization | 04/05/2026 | 04/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Hands-on IAM & Cloud9:** Launched a Cloud9 environment, created an IAM Role with minimal S3 access, attached it to an EC2 instance, and verified temporary credential generation via CLI | 05/05/2026 | 05/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Researched Amazon S3 architecture: Buckets, Objects, S3 Block Public Access, Bucket Policies, and ACLs <br> - Configured S3 Static Website Hosting with custom error pages and index pages | 06/05/2026 | 06/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Studied Relational Database Service (RDS) concepts: Engine selection (MySQL/PostgreSQL), Multi-AZ deployments, and DB Subnet Groups | 07/05/2026 | 07/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Hands-on RDS & Web Integration:** Provisioned an RDS MySQL instance inside isolated private subnets, attached DB Security Groups, and successfully established a remote connection from an EC2 web app | 08/05/2026 | 08/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Week 3 Insights & Reflections:

* **IMDSv2 and Temporary Credentials via IAM Roles:**
  * **How EC2 gets AWS credentials without hardcoding:** Attaching an IAM Role to an EC2 instance creates an *Instance Profile*. The instance queries the internal Instance Metadata Service at `http://169.254.169.254/latest/meta-data/iam/security-credentials/` to retrieve short-lived access keys provided by AWS Security Token Service (STS).
  * **Why IMDSv2 is crucial:** IMDSv2 requires a session token fetched via a `PUT` request before making `GET` metadata requests. This session-oriented mechanism neutralizes SSRF (Server-Side Request Forgery) vulnerabilities that could otherwise leak credentials if an application running on EC2 is compromised.

* **Amazon S3 Security Boundary & Static Web Hosting Mechanics:**
  * **Block Public Access Safety Valve:** AWS S3 applies four "Block Public Access" settings at the account and bucket levels by default. To host a public static website, I learned that disabling Block Public Access alone is insufficient—one must explicitly grant read access (`s3:GetObject`) through a scoped JSON Bucket Policy.
  * **Object vs. Block Storage:** Unlike EBS (block storage attached to a single instance operating at the OS filesystem level), S3 is a flat object store accessed over HTTPS APIs. It provides near-infinite scale and built-in 99.999999999% (11 9's) durability by redundantly replicating data across multiple Availability Zones.

* **Database Isolation and Security Group Chaining in Amazon RDS:**
  * **DB Subnet Group Isolation:** An RDS instance must be assigned a DB Subnet Group encompassing subnets across at least two Availability Zones. For production security, these subnets must be strictly *private* (no routes to an IGW), rendering the database unreachable directly from the public internet.
  * **Security Group Chaining (Least Privilege Networking):** Instead of whitelisting specific IP ranges (e.g., `10.0.1.0/24`) in the RDS Security Group inbound rules, the best practice is *referencing the Web Application's Security Group ID directly* (e.g., `sg-web-app`). This allows inbound MySQL traffic (port 3306) *only* from EC2 instances assigned to that specific Web SG, automatically accommodating scaling events without manual IP updates.

* **Cloud-Native Development Efficiency with Cloud9:**
  * Using AWS Cloud9 provided a pre-configured Ubuntu/Amazon Linux environment with the AWS CLI, Docker, and git pre-installed.
  * The seamless integration between Cloud9 and managed credentials simplifies testing multi-tier architectures without polluting local workstation configurations.