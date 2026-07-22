---
title: "Workshop"
date: 2026-07-12
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

#### Overview

This document is a worklog detailing the AWS architecture deployed for **Maison Édition** — a fashion storefront whose frontend is served from S3 + CloudFront and whose backend runs on EC2 behind an Application Load Balancer with an RDS PostgreSQL database. On top of that baseline, the system adds six capabilities, each closing a specific gap in the baseline: S3 Media storage (a dedicated place to store and serve product images instead of the app server), a Redis cache (cuts read load on RDS for hot catalog data), asynchronous order processing with SQS + Lambda (keeps checkout responsive instead of blocking on email/logging), an activity log on DynamoDB (an audit trail of user actions), monitoring with CloudWatch + SNS (early warning when something breaks), and a WAF at the edge (blocks attacks before they reach the application).

{{% notice tip %}}
**🔗 Experience the live application at:** [https://d1tz40a6c8kj4v.cloudfront.net/](https://d1tz40a6c8kj4v.cloudfront.net/)
{{% /notice %}}

**▶️ Demo video:**
{{< youtube oFeH611LTfE >}}

Each chapter records what a service does and how it was inspected and tested directly on the AWS Console.

```
User Request
    │
    ▼
AWS WAF ──── Blocks SQLi/XSS, rate limit 2000 req/5min/IP
    │
    ▼
AWS CloudFront
    ├── /api/*   ──────────────────────────────► ALB (HTTP:80) ──► EC2 x2 (Spring Boot)
    ├── /media/* ──► S3 Media Bucket (product images)                  │
    └── /*       ──► S3 FE Bucket (React build)                        ├──► RDS PostgreSQL
                                                                       ├──► ElastiCache Redis (cache)
                                                                       ├──► S3 (image upload, via Gateway Endpoint)
                                                                       └──► SQS ──► Lambda ──► SES (email) + DynamoDB (activity log)

CloudWatch (metrics + logs) ──► SNS ──► Email alerts
```

#### Content

1. [Architecture overview](5.1-Architecture-Overview/)
2. [Prerequisites](5.2-Preparation/)
3. [S3 Media + Gateway Endpoint](5.3-S3-Media-Gateway-Endpoint/)
4. [ElastiCache Redis](5.4-ElastiCache-Redis/)
5. [SQS + Lambda — order processing](5.5-SQS-Lambda-Order/)
6. [DynamoDB — Activity Log](5.6-DynamoDB-Activity-Log/)
7. [CloudWatch + SNS — Monitoring](5.7-CloudWatch-SNS/)
8. [WAF](5.8-WAF/)
9. [Cost & Cleanup](5.9-Cost-Cleanup/)
