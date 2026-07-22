---
title: "Week 5 Worklog"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---



### Week 5 Objectives:

* Implement centralized monitoring, log aggregation, and automated alerting for system resources using Amazon CloudWatch.
* Master domain name resolution (DNS) mechanisms, record types, and routing policies using Amazon Route 53.
* Configure Private Hosted Zones for internal VPC DNS resolution and build resilient hybrid DNS architectures.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Studied CloudWatch architecture: Metrics, Alarms, Logs, and the CloudWatch Unified Agent <br> - Analyzed host-level hypervisor metrics vs. OS-level custom metrics | 18/05/2026 | 18/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Hands-on CloudWatch:** Installed CloudWatch Agent on EC2 to collect RAM/Disk usage, streamed system logs to CloudWatch Log Groups, and configured SNS email alerts for CPU spikes | 19/05/2026 | 19/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Researched Amazon Route 53 DNS fundamentals: Record Types (A, AAAA, CNAME, MX, TXT, Alias) and Public vs. Private Hosted Zones | 20/05/2026 | 20/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Analyzed Route 53 Advanced Routing Policies: Simple, Weighted, Latency-based, Failover (Health Check integrated), and Geolocation routing | 21/05/2026 | 21/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Hands-on Route 53:** Created a Public Hosted Zone mapping a domain to an ALB via Alias records, and established a Private Hosted Zone for private VPC domain resolution | 22/05/2026 | 22/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Week 5 Insights & Reflections:

* **Hypervisor Metrics vs. OS-Level Custom Metrics in CloudWatch:**
  * **The Hypervisor Visibility Limit:** By default, AWS CloudWatch monitors EC2 instances from the physical hypervisor layer. This provides visibility into CPU Utilization, Network I/O, and Disk Read/Write operations, but has zero insight into guest OS internal state.
  * **Why CloudWatch Agent is Essential:** Memory (RAM) utilization and available disk space operate strictly inside the Linux kernel/OS memory management layer. To collect these critical metrics, installing and configuring the CloudWatch Unified Agent inside the instance is mandatory to push custom metrics via AWS APIs.

* **Log Centralization and Proactive Incident Response via SNS:**
  * **Decoupling Logs from Ephemeral Disks:** Streaming application logs (`/var/log/nginx/access.log`, system journals) directly to CloudWatch Log Groups prevents log loss when instances are terminated by Auto Scaling or hardware faults.
  * **Automated Alarm Wiring:** Integrating CloudWatch Alarms with Amazon Simple Notification Service (SNS) transforms operations from reactive log checking to proactive alerting. When a metric threshold breaches (e.g., CPU > 85% for 5 minutes), SNS triggers email notifications or PagerDuty webhooks immediately.

* **Route 53 Alias Records vs. Standard CNAME Records:**
  * **Zone Apex Constraint (`example.com`):** Standard DNS specifications (RFC 1034) prohibit CNAME records at the root/apex domain level if other records exist.
  * **The Power of Route 53 Alias Records:** Alias records are a proprietary AWS extension that seamlessly maps root domain names (`example.com`) directly to AWS resources (ALB, CloudFront distribution, S3 bucket) without CNAME limitations. Furthermore, Route 53 queries to Alias records for AWS resources are free of charge.

* **Private Hosted Zones for Enterprise Security & Hybrid DNS:**
  * Using Route 53 Private Hosted Zones allows defining custom internal domain names (e.g., `db.internal.company.local`) scoped strictly to designated VPCs.
  * This eliminates the need to expose private IP addresses in public DNS records, ensuring that internal microservice endpoint discovery remains entirely hidden from the public internet.