---
title: "Workload Discovery on AWS – The Smart Cloud Architecture Mapping Tool!"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


Hi everyone, I am an intern at AWS. During my time here, I had the opportunity to deep dive into **Workload Discovery on AWS** (formerly known as AWS Perspective) – an extremely useful solution for visualizing workloads on AWS. Today, I'd like to share the core knowledge about its architecture.

### What is Workload Discovery on AWS?
This tool helps you:
* **Automatically gather AWS resource inventory** across multiple Accounts & Regions.
* **Draw detailed architecture diagrams** based on live data.
* **Understand relationships** between resources clearly.
* **Easily build, customize, and share** diagrams.
* **Support cost analysis** to view resource costs.

---

### Architecture Overview
When deploying this solution using CloudFormation (default), it creates an environment with the following key components:

#### 1. Web UI (User Interface)
* Hosted on Amazon S3 + distributed via Amazon CloudFront.
* Secured by Amazon Cognito (user authentication).
* Protected by AWS WAF.

#### 2. Backend & API
* Uses AWS AppSync as the main GraphQL API.
* Authenticated using JWTs from Cognito.

#### 3. Data Layer
* **Amazon Neptune (Graph Database)**: Stores resource relationships.
* **Amazon OpenSearch Service**: Provides powerful search capabilities.
* **Amazon DynamoDB**: Stores configurations and settings.

#### 4. Discovery Component (Core)
* Runs on AWS Fargate (ECS) with a cron schedule running every 15 minutes.
* Uses AWS Config + AWS SDK to scan inventory.
* Saves data to Neptune & OpenSearch via AppSync.

#### 5. Cost Analysis
* Uses Amazon Athena to query AWS Cost and Usage Report (CUR).
* Helps display estimated resource costs on the diagram.

> The entire architecture is designed to be serverless-friendly, secure, and scalable.

![Workload Discovery on AWS Architecture](/images/3-BlogsPosted/image1.png)

---

### Why I Highly Recommend This Tool
* Helps you quickly understand the big picture of your system without manual drawing.
* Interactive diagrams: drag and drop, zoom, group by VPC, AZ, Account, etc.
* Always updated near real-time.
* Can export/share beautiful diagrams with your team or customers.

If you are managing AWS infrastructure, I highly recommend trying this solution!

* **Official Documentation:** [Architecture Overview Workload Discovery on AWS](https://docs.aws.amazon.com/solutions/latest/workload-discovery-on-aws/architecture-overview.html)