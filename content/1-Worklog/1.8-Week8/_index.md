---
title: "Week 8 Worklog"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:
* Master enterprise directory services and centralized identity management by provisioning and configuring AWS Managed Microsoft AD.
* Build an advanced multi-layered observability platform by integrating Amazon CloudWatch telemetry metrics and log groups with Grafana dashboards.
* Evaluate, iterate, and optimize the preliminary AWS multi-AZ architecture draft created in Week 7 to align with Well-Architected Framework principles (Reliability, Performance Efficiency, and Cost Optimization).

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Researched Active Directory architecture on AWS, comparing AWS Managed Microsoft AD, AD Connector, and Simple AD to select the right directory strategy | 08/06/2026 | 08/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Hands-on AWS Managed Microsoft AD:** Provisioned AWS Managed Microsoft AD, configured custom DHCP Option Sets, joined EC2 instances to the domain, and verified centralized authentication | 09/06/2026 | 09/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - **Hands-on Observability & Grafana Integration:** Deployed CloudWatch Unified Agent on EC2 instances for OS-level metrics, integrated CloudWatch into Grafana via IAM Role assume policies, and built visual monitoring dashboards | 10/06/2026 | 10/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Evaluated Week 7's architecture sketch against workload requirements, identifying bottlenecks, single points of failure (SPOFs), and unoptimized NAT/DB costs | 11/06/2026 | 11/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Hands-on Architecture Optimization:** Refined the project blueprint by adding RDS Read Replicas, integrating ElastiCache Redis for hot data caching, tuning Auto Scaling triggers, and optimizing multi-AZ subnet routing | 12/06/2026 | 12/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Lessons Learned & Key Architectural Insights:

* **Enterprise Directory Services & The Practical "Why" Behind AWS Managed Microsoft AD:**
  * **Self-Hosted EC2 vs. Managed Directory Service:** When faced with setting up Active Directory on AWS, the initial impulse is often to spin up standard Windows Server EC2 instances and install AD DS manually. However, doing so introduces massive operational overhead: managing DC domain replication across subnets, configuring automated backups, orchestrating OS security patching, and ensuring multi-AZ high availability manually. AWS Managed Microsoft AD deploys actual native Microsoft AD running on managed Domain Controllers across multiple Availability Zones, letting AWS handle infrastructure maintenance while giving administrators full GPO, LDAP, Kerberos, and RSAT control.
  * **The Critical Role of DHCP Option Sets in Domain Joining:** A key troubleshooting takeaway during execution was realizing that network connectivity alone does not guarantee a successful Domain Join for EC2 instances. By default, AWS VPCs assign `AmazonProvidedDNS` to instances, which only resolves public/VPC internal addresses. To allow Windows and Linux instances to locate Kerberos domain controllers via SRV records, custom VPC DHCP Option Sets must be created and linked to the VPC with the exact IP addresses of the Managed AD domain controller interfaces. Without this adjustment, domain join attempts silently time out.

* **Building Enterprise Observability: Bridging the Telemetry Gap with CloudWatch & Grafana:**
  * **Understanding Hypervisor vs. Guest OS Metrics:** Default Amazon CloudWatch metrics operate strictly at the Hypervisor level—measuring external physical indicators like CPU Utilization, Network In/Out, and Disk Read/Write Ops. However, the Hypervisor cannot inspect the memory state or internal filesystem of the Guest OS. As a result, critical issues like RAM exhaustion (Out-Of-Memory kills) or full disk partitions are completely invisible to standard CloudWatch. Installing the CloudWatch Unified Agent inside the EC2 operating system bridges this gap, allowing custom metrics (RAM usage, swap usage, disk space percentage) and application log files to flow directly into CloudWatch Log Groups.
  * **Decoupling Collection from Visualization (CloudWatch + Grafana):** While CloudWatch is an excellent log collector and alarm trigger engine, Grafana excels at high-density, real-time visualization and multi-source correlation. Integrating CloudWatch as a data source in Grafana using IAM Role Assume policies (via Service Accounts/Instance Profiles) avoids hardcoding long-term API credentials while creating flexible operational dashboards. This establishes a clear separation of concerns: CloudWatch handles telemetry collection and log retention, while Grafana delivers unified visual analytics.

* **Evolving the Project Blueprint: Shifting from a Functional Sketch to Production Readiness:**
  * **Offloading Database Pressure via Caching and Query Splitting:** In the preliminary Week 7 architecture, the database tier relied solely on a single primary RDS instance. Under heavy application traffic, concurrent database connections and disk IOPS limits quickly become major bottlenecks. In Week 8, we optimized this by adding an Amazon ElastiCache (Redis) cluster in front of the database tier to handle session state management and cache frequently read data with microsecond latency. Additionally, introducing RDS Read Replicas routes read-only queries away from the primary instance, preserving primary RDS CPU/IOPS strictly for transactional write operations.
  * **Balancing Fault Tolerance against Infrastructure Costs:** Architectural refinement is an ongoing exercise in trade-offs. For example, deploying redundant NAT Gateways in every private subnet across multiple AZs maximizes fault isolation, but incurs significant hourly baseline charges. Through careful workload assessment, we optimized subnet routing and security group boundaries so that private workloads maintain Multi-AZ failover resilience without incurring unnecessary cross-AZ data transfer overhead or redundant NAT costs during early project phases.