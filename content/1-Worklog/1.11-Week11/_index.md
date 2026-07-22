---
title: "Week 11 Worklog"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:
* Master cloud financial modeling using the AWS Pricing Calculator to build comprehensive Total Cost of Ownership (TCO) estimates and formulate cost-optimization strategies.
* Conduct a thorough security and risk audit across the master AWS architecture aligned with the Well-Architected Security Pillar.
* Propose actionable security hardening measures and risk mitigation strategies to protect system assets before final project handover.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Researched AWS pricing models (On-Demand, Compute Savings Plans, Reserved Instances, Spot Instances) and storage tiering options | 29/06/2026 | 29/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Hands-on AWS Pricing Calculator:** Modeled monthly baseline costs for the master architecture (EC2 Auto Scaling, RDS Multi-AZ, ElastiCache, CloudFront, ALB, and NAT Gateways) | 30/06/2026 | 30/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Formulated cost optimization strategies: Designed S3 Lifecycle rules (Glacier Instant Retrieval), tuned Auto Scaling schedule triggers, and identified rightsizing targets | 01/07/2026 | 01/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - **Hands-on Security & Risk Audit:** Conducted a comprehensive risk review using AWS Security Hub concepts, IAM Access Analyzer, and Security Group ingress/egress audits | 02/07/2026 | 02/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Security Hardening Implementation:** Enabled VPC Flow Logs for network threat analysis, tightened Security Group boundaries, enforced TLS 1.3 across edge endpoints, and finalized the security risk mitigation report | 03/07/2026 | 03/07/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Lessons Learned & Key Architectural Insights:

* **Financial Engineering & Cloud Cost Modeling via AWS Pricing Calculator:**
  * **Uncovering Hidden Cloud Cost Drivers:** A key realization during cost modeling was that primary infrastructure costs extend far beyond standard EC2 compute and RDS database instances. Data Transfer Out (DTO) to the internet, NAT Gateway hourly rates combined with per-GB data processing fees, and provisioned EBS IOPS often account for unexpected billing spikes.
  * **Strategic Cost Optimization without Compromising Reliability:** Building the TCO model highlighted the power of combining multiple commitment models. For baseline compute workloads running 24/7 (such as primary database instances and minimum Auto Scaling instances), committing to 1-year Compute Savings Plans yields up to a 40–60% discount over On-Demand rates. Furthermore, implementing S3 Lifecycle Policies to automatically transition access logs and database backups from S3 Standard to S3 Glacier Instant Retrieval after 30 days curbs long-term storage growth costs significantly.

* **Security Risk Audit & The Well-Architected Security Pillar:**
  * **Continuous Threat Modeling vs. Static Compliance:** Conducting a comprehensive risk audit demonstrated that cloud security is a continuous operational practice rather than a static setup. Auditing security configurations revealed common micro-vulnerabilities—such as overly permissive egress rules on internal Security Groups, unencrypted S3 log buckets, or legacy TLS protocol support on load balancer listeners.
  * **Observability-Driven Defense with VPC Flow Logs:** Implementing VPC Flow Logs and auditing network boundaries provided deep visibility into unexpected inter-subnet traffic patterns. By restricting Security Group ingress rules strictly to referenced Security Group IDs (Security Group Chaining) rather than broad CIDR blocks, we eliminated internal exposure risks and established zero-trust network segmentation.

* **The Delicate Balance: Cost Optimization vs. High Availability & Security:**
  * **Avoiding Over-Engineering and Under-Protection:** Cloud architecture involves a constant tug-of-war between financial constraints and architectural resilience. Over-provisioning infrastructure guarantees performance but drains budgets, while aggressive cost-cutting risks single points of failure (SPOFs) or security breaches.
  * **Finding the Optimal Trade-Off:** The project demonstrated that smart architecture choices—such as replacing costly NAT Gateway data transfer with VPC Endpoints for S3/KMS, or using Auto Scaling warm pools instead of statically running large EC2 instances 24/7—achieve both strict security compliance and cost efficiency simultaneously.