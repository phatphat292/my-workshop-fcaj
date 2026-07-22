---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
## A Comprehensive AWS Cloud Solution for Secure, Scalable, and Automated Online Operations

### 1. Executive Summary
This project delivers a high-performance, secure, and auto-scaling E-commerce web infrastructure designed for a premium fashion retail brand. Built entirely on AWS Cloud, the system is engineered to seamlessly handle thousands of concurrent online shoppers, automatically scaling compute capacity from 2 up to 4 EC2 instances via an Auto Scaling Group with CPU-based target tracking (60% average utilization) during traffic spikes. By leveraging an AWS multi-tier architecture, the platform guarantees ultra-low latency shopping experiences, automated cart processing, and maximum data protection via Amazon Cognito and AWS WAF integration.

### 2. Problem Statement
* **Current Challenges:** Traditional monolithic web hosting infrastructures for fashion stores frequently suffer from server crashes and network congestion during seasonal sales traffic spikes. Furthermore, they lack real-time cart analysis capabilities, rely on manual inventory adjustments, remain highly vulnerable to cyber-attacks (DDoS, SQL Injection), and incur high, fixed maintenance costs for physical servers even during off-peak months.
* **The Solution:** The proposed platform implements a secure, decoupled Multi-Tier Architecture on AWS:
    * **Edge & Security Tier:** Customers access the web storefront via **AWS CloudFront (CDN)** for rapid page loading by fetching static web elements from **Amazon S3**. This layer is heavily protected by **AWS WAF** to inspect and block malicious web requests.
    * **Compute & Load Balancing Tier:** HTTP Requests transition through the **Internet Gateway** to an **Application Load Balancer (ALB)**, which evenly routes incoming traffic to **Amazon EC2** instances across multiple Availability Zones (AZs) inside Private Subnets to guarantee High Availability.
    * **Data & Caching Tier:** Product details, transactional data, and user accounts are securely managed in a Multi-AZ **Amazon RDS** relational database cluster. Real-time session state and shopping cart actions are accelerated using **AWS ElastiCache (Redis)** for sub-millisecond read/write speeds.
    * **Serverless Asynchronous Processing Tier:** New orders are safely pushed into an **AWS SQS** queue, triggering **AWS Lambda** functions to process purchases asynchronously without putting stress on the core web servers. Historical transactions and telemetry logs are dumped into **Amazon DynamoDB** and **Amazon S3** via **S3 Gateways**.
* **Benefits and Return on Investment (ROI):**
    * **Cost Efficiency:** Driven by Auto Scaling Groups (ASG), the business only pays for the active compute resources required at any given minute, dropping operational costs during late-night hours.
    * **Higher Conversion Rates:** Blazing-fast website performance (<1s load time) powered by CloudFront and ElastiCache enhances user engagement and slashes cart abandonment rates.
    * **Uncompromised Security:** Automatic mitigation of 99% of common web vulnerabilities and brute-force attempts at the network edge via WAF rules.
    * **Payback Period:** ROI achieved within 3–6 months due to zero upfront physical hardware capital expenditures and a nationwide boost in automated digital sales.

---

### 3. Solution Architecture
The platform enforces a highly available, fault-tolerant design on AWS, leveraging distinct Public and Private Subnets to completely isolate core data and compute resources from the open internet.

**System Architecture Model**
![E-commerce System Architecture](/images/2-Proposal/mohinh.png)

#### AWS Services Utilized
* **AWS WAF & CloudFront:** Edge web security, malicious payload filtering, and global static/dynamic content delivery.
* **Amazon VPC (Public/Private Subnets & NAT Gateways):** Internal isolated network topologies. NAT Gateways allow EC2 nodes inside Private Subnets to safely retrieve package updates without exposing their private IPs.
* **Application Load Balancer (ALB) & EC2:** Traffic distribution and core application business logic execution.
* **AWS ElastiCache:** Caching tier for instant shopping cart states and trending product items.
* **Amazon RDS:** Primary relational database (handling user records, inventory tracking, and payment invoices).
* **AWS SQS & AWS Lambda:** Serverless microservices to ingest and handle payment events asynchronously.
* **Amazon DynamoDB & S3:** Highly scalable NoSQL engine for system event logging/browsing histories, and durable asset storage for high-definition product imagery.
* **AWS CloudWatch & AWS SNS:** Comprehensive infrastructure metric monitoring (CPU, Memory, Network) coupled with instant administrative notification pipelines (Email/SMS Alerts).

---

### 4. Technical Implementation
#### Implementation Phases
1. **Phase 1: Architecture Topology & Database Schema Design (Month 1):** Mapping out precise VPC configurations, Security Group restrictions, IAM Roles, and defining relational database structures optimized for retail transactions.
2. **Phase 2: Infrastructure as Code (IaC) Provisioning (Month 2):** Writing automated Terraform or AWS CDK scripts to easily instantiate the ALB, EC2 Auto Scaling environments, RDS instances, and CloudFront routing behaviors.
3. **Phase 3: Backend Assembly & Serverless Integration (Month 2 - Month 3):** Hooking the web backend code into ElastiCache endpoints, establishing Lambda triggers to drain SQS queues, and structured log archiving into DynamoDB.
4. **Phase 4: Load Testing & Production Go-Live (Month 3):** Simulating up to 10,000 concurrent shopping interactions using load-testing scripts to validate the automated scalability boundaries, finalizing WAF rule sets, and redirecting the production DNS.

#### Technical Requirements
* **Web Application Stack:** Spring Boot (Java) containerized via Docker, deployed seamlessly onto EC2 instances via standardized Auto Scaling Launch Templates.
* **Data Connectivity Performance:** Maintaining ultra-low communication latency (<5ms) between EC2 compute nodes, RDS instances, and ElastiCache backends by restricting communications to local AZ clusters and configuring dedicated VPC Endpoints (S3 Gateway).

---

### 5. Roadmap & Milestones
* **Month 1:** Complete granular system topology maps, initialize the master AWS Organizations account, and implement strict IAM least-privilege policies.
* **Month 2:** Stand up the Staging environment containing the complete network architecture (VPC, EC2, RDS, Caching, SQS) and execute functional cart checkouts.
* **Month 3:** Tie together all remaining serverless features (Lambda, DynamoDB logs), build out CloudWatch alert alarms linked to SNS notifications, complete stress tests, and transition the stack into live production.

---

### 6. Budget Estimation
* **NAT Gateway (x2, one per AZ):** The single largest cost driver, covering outbound internet access for EC2 instances in the private subnets (~$85 - $90/month).
* **Amazon EC2 (t3.micro instances, Auto Scaling Group 2-4) & ALB:** Baseline of 2 nodes across distinct AZs for high availability, auto-scaling up to 4 under sustained CPU load (~$40 - $60/month depending on scale).
* **Amazon RDS (db.t3.micro - Multi-AZ Deployment):** Safe, mirrored production database instance (~$35/month).
* **AWS ElastiCache (Redis, cache.t3.micro):** Catalog caching node (~$13 - $15/month).
* **AWS WAF:** Web ACL plus 4 managed rule groups protecting the edge (~$9/month).
* **Amazon S3, CloudFront, DynamoDB, SQS, Lambda, SES, SNS & CloudWatch:** Event-driven / usage-based billing, mostly absorbed by the AWS Free Tier (~$2 - $3/month).

> **Total Estimated Monthly Cost:** ~$184 - $212/month (baseline ~$184-192 at 2 EC2 instances, rising toward $212 only during the hours the Auto Scaling Group actually scales out to its max of 4 — not a flat monthly addition. Further cost optimizations can be achieved by opting for EC2/RDS Reserved Instances or Savings Plans, or by reducing to a single NAT Gateway).

---

### 7. Risk Assessment
#### Risk Matrix
* **Distributed Denial of Service (DDoS) / Brute Force Web Attacks:** High Impact, Medium Probability.
* **Cart Synchronization Errors / Phantom Inventory Race Conditions:** High Impact, Low Probability.
* **AWS Budget Overruns Due to Misconfigured Auto Scaling Loops:** Medium Impact, Medium Probability.

#### Mitigation Strategies & Contingency Planning
* **DDoS Defenses:** Stopped at the outermost layer by AWS WAF managed rule groups (Common Rule Set, SQL Injection, Known Bad Inputs) combined with a rate-based rule blocking any single IP that exceeds 2,000 requests per 5 minutes.
* **Data Integrity:** Order records are written synchronously to RDS at checkout — before any queue is involved — so a database transaction, not queue ordering, prevents double-checkout. A standard **AWS SQS** queue then decouples email/logging from the checkout response, with a dead-letter queue (max 3 delivery attempts) isolating failed messages instead of dropping them silently.
* **Cost Protections:** Establishing proactive **AWS Budgets** boundaries that automatically push warnings via **AWS SNS** alerts directly to the engineering team's communications channels the second monthly costs cross 80% of forecasted targets.

---

### 8. Expected Outcomes
* An enterprise-grade, highly available online storefront maintaining a 99.99% application uptime SLA.
* The capability to seamlessly endure a 5x spike in concurrent shoppers compared to the company’s heritage setup, while generating up to a 40% reduction in average running overhead by spinning down unneeded instances when web traffic drops.
* A highly organized, centralized data engine (via S3 and DynamoDB logs) tracking historical buyer journeys, providing the brand with a foundation to run machine-learning product recommendation models and predictive fashion trend algorithms in subsequent business expansions.