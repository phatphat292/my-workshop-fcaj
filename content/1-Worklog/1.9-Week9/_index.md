---
title: "Week 9 Worklog"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:
* Design and implement fine-grained access control policies using AWS IAM and Attribute-Based Access Control (ABAC) driven by Resource Tags.
* Integrate core supporting AWS security and network services—including AWS KMS, Secrets Manager, AWS WAF, and VPC Endpoints—into the overall architecture blueprint.
* Map out explicit service interconnectivity, private data pathways, and perimeter security boundaries within the multi-AZ project infrastructure.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Researched Attribute-Based Access Control (ABAC) vs. Role-Based Access Control (RBAC) in IAM, focusing on tag-based authorization keys (`aws:ResourceTag`, `aws:RequestTag`) | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Hands-on IAM & Tag Governance:** Drafted fine-grained IAM policies enforcing tag matching (e.g., `Environment=Production`, `Owner=PhatPH`), tested permission boundaries, and enforced mandatory tagging rules | 16/06/2026 | 16/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Researched private connectivity and encryption services: VPC Endpoints (Gateway vs. Interface Endpoints), AWS KMS customer managed keys (CMK), AWS Secrets Manager, and AWS WAF web ACLs | 17/06/2026 | 17/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - **Hands-on AWS Service Integration:** Configured S3 Gateway Endpoints and Secrets Manager Interface Endpoints, created custom KMS keys for RDS envelope encryption, and attached WAF rules to CloudFront/ALB | 18/06/2026 | 18/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Hands-on Architecture Integration:** Expanded the project architecture blueprint to explicitly illustrate security boundaries, private VPC Endpoint routing, KMS key flows, and WAF perimeter defenses | 19/06/2026 | 19/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Bài học & góc nhìn rút ra:

* **Scalable Permission Governance: Moving from RBAC to ABAC via Resource Tags:**
  * **The Operational Bottleneck of RBAC at Scale:** In growing cloud environments, traditional Role-Based Access Control (RBAC) requires creating and updating distinct IAM roles and policies for every new team, project, or environment. This creates severe policy sprawl and administrative overhead.
  * **Why ABAC is the Enterprise Solution:** Implementing Attribute-Based Access Control (ABAC) allows authorization based on tags attached to IAM principals and AWS resources. By writing a single IAM condition that compares `${aws:PrincipalTag/Department}` with `${aws:ResourceTag/Department}`, permissions scale automatically as new resources are created without modifying IAM policies.
  * **Enforcing Tag Governance at Provisioning:** A crucial practical insight was enforcing tag compliance using the `aws:RequestTag` condition key. By denying `ec2:RunInstances` or `s3:CreateBucket` actions unless required environment and owner tags are supplied at creation time, we prevent untagged "orphan" resources from bypassing governance policies or inflating infrastructure costs.

* **Securing Internal Data Pathways with VPC Endpoints & KMS Envelope Encryption:**
  * **Eliminating Public Internet Traversal via VPC Endpoints:** By default, when an EC2 instance in a private subnet communicates with public AWS service endpoints like Amazon S3, AWS Secrets Manager, or KMS, traffic routes through NAT Gateways over the public internet. Deploying Gateway Endpoints (for S3 and DynamoDB) and Interface Endpoints / AWS PrivateLink (for Secrets Manager and KMS) keeps all data traffic strictly within the private AWS network backbone. This significantly improves network security, lowers latency, and eliminates NAT Gateway data processing charges.
  * **Decoupling Secrets from Code & Centralized KMS Encryption:** Storing database credentials or API keys directly in configuration files introduces major security vulnerabilities. Integrating AWS Secrets Manager allows application instances to retrieve encrypted credentials dynamically at runtime using IAM roles. Combining this with custom KMS Customer Managed Keys (CMKs) ensures envelope encryption at rest for RDS databases, S3 buckets, and EBS volumes with full CloudTrail audit logging.

* **Architectural Integration: Establishing Defense-in-Depth Across System Layers:**
  * **Layered Perimeter Defense:** Updating the architecture blueprint emphasized the necessity of a "Defense-in-Depth" strategy. AWS WAF attached at the CloudFront/ALB tier shields application servers from common web exploits (SQL injection, XSS, rate-limiting HTTP floods). Security Groups and Network ACLs strictly restrict inter-tier traffic at the network layer, while KMS and IAM enforce security at the data and identity layers.
  * **Explicit Mapping over Implicit Assumptions:** A key realization when refining the architecture diagram was that every supporting service—such as secret retrieval pathways, DNS resolution routes, and private endpoint links—must be explicitly mapped. Vague connections in initial drafts often mask critical security oversights or routing bottlenecks that surface during production deployment.