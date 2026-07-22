---
title : "Architecture overview"
date : 2026-07-12
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Two layers, one origin

The system was designed with two layers:

+ **Frontend layer** — a static React build served from S3, distributed
  through CloudFront (CDN + HTTPS), and protected by WAF.
+ **Backend layer** — a Spring Boot application running on EC2 (in an Auto
  Scaling Group, 2-4 instances) behind an Application Load Balancer, with RDS
  PostgreSQL as its main database and all the supporting services (S3 Media,
  ElastiCache, SQS/Lambda, DynamoDB, CloudWatch/SNS).

CloudFront was configured to route the `/api/*` path straight to the backend's ALB. Because of this, the browser always talks to a single origin (`d1tz40a6c8kj4v.cloudfront.net`) — the frontend and backend never trigger a CORS problem even though they run on completely separate services.

The overall architecture looks like this:
![mohinh](/images/5-Workshop/5.1-Architecture-Overview/mohinh.png)

#### Network configuration

The network layer is set up with:
+ 1 VPC across 2 Availability Zones (`ap-southeast-1a`, `ap-southeast-1b`).
+ 2 public subnets (ALB + NAT Gateway), 2 private subnets (EC2 + RDS).
+ 2 NAT Gateways (1 per AZ) — the single biggest cost driver in the whole system, as discussed in the [Cost](../5.9-Cost-Cleanup/) chapter.
+ 1 **S3 Gateway VPC Endpoint** — allows EC2 to reach S3 without going through NAT.

**Inspecting Resource Map and EC2 Instances:**
- On the VPC console, under **Resource map**, every subnet, route table, and gateway showed the correct connections.
![VPC](/images/5-Workshop/5.1-Architecture-Overview/VPC.png)
- On the EC2 console, under **Auto Scaling Groups**, `fashion-shop-prod-asg` showed 2 instances `InService` (min 2 / desired 2 / max 4, target-tracking on 60% average CPU), both registered as `healthy` targets on the ALB.
![ALB](/images/5-Workshop/5.1-Architecture-Overview/ALB.png)

#### Services in use

| Group | Service | Role |
|---|---|---|
| Compute | EC2 (Auto Scaling Group, 2-4), ALB | Run & load-balance the application, auto-scale on CPU |
| Frontend | S3, CloudFront, WAF | Serve the storefront + CDN + block attacks |
| Data | RDS PostgreSQL, ElastiCache Redis, DynamoDB | Business DB / cache / activity log |
| Async | SQS, Lambda, SES | Background order processing, sending email |
| Monitoring | CloudWatch, SNS | Logs, alarms, email alerts |
