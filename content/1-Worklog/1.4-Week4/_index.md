---
title: "Week 4 Worklog"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---



### Week 4 Objectives:

* Explore simplified compute options using Amazon Lightsail and deploy containerized microservices via Lightsail Container Services.
* Master EC2 Auto Scaling Group (ASG) mechanisms to achieve high availability, elasticity, and fault tolerance across multiple Availability Zones.
* Integrate Application Load Balancer (ALB) health checks with ASG to construct self-healing application infrastructure.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Studied Amazon Lightsail compute architecture and evaluated trade-offs between Lightsail (flat-rate simplicity) and Amazon EC2 (granular control) | 11/05/2026 | 11/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Hands-on Lightsail Containers:** Containerized a web application with Docker, pushed the image to Lightsail, and configured deployment power/scale settings | 12/05/2026 | 12/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Researched EC2 Auto Scaling fundamentals: Launch Templates, Auto Scaling Groups (ASG), Minimum/Maximum/Desired Capacity, and Scaling Policies | 13/05/2026 | 13/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Analyzed Application Load Balancer (ALB) target group integration with ASG and contrasted EC2 status checks vs ELB health checks | 14/05/2026 | 14/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Hands-on ASG & High Availability:** Configured a multi-AZ Launch Template, created an ASG connected to an ALB, and simulated high CPU loads to trigger Target Tracking scaling | 15/05/2026 | 15/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Week 4 Insights & Reflections:

* **Amazon Lightsail Containers vs. Enterprise Container Orchestration:**
  * **Simplified Container Deployment:** Amazon Lightsail Container Services offer a frictionless path to running Docker containers in the cloud without needing to manage Kubernetes clusters (EKS) or complex task definitions (ECS). It automatically handles SSL/TLS certificates and load balancing under a predictable monthly pricing model.
  * **Architectural Boundaries:** While Lightsail is ideal for small-to-medium applications and rapid prototyping, large enterprise workloads require standard EC2/ECS/EKS setups to leverage granular IAM policies, advanced VPC peering, and custom auto-scaling metrics.

* **Elasticity vs. High Availability in EC2 Auto Scaling:**
  * **High Availability (Multi-AZ Deployment):** Distributing instances across multiple Availability Zones (AZs) protects against physical datacenter outages. If an entire AZ experiences a failure, the Auto Scaling Group (ASG) automatically provisions replacement instances in the remaining healthy AZs to preserve the `Desired Capacity`.
  * **Elasticity (Scaling Policies):** Implementing Target Tracking Scaling (e.g., maintaining average CPU utilization at 60%) ensures capacity dynamically expands during traffic surges and contracts during lulls, directly optimizing infrastructure cost efficiency.

* **Self-Healing Mechanics: ELB Health Checks vs. EC2 Status Checks:**
  * **Hardware/Hypervisor vs. Application Health:** Default EC2 Status Checks only detect system/instance hardware degradation (e.g., hypervisor loss). However, if the web server process (e.g., Nginx or Node.js) crashes inside the OS, the EC2 instance status remains "Passed".
  * **Enabling ELB Health Checks on ASG:** By switching the ASG health check type to `ELB`, the ASG monitors actual HTTP status codes (e.g., checking for `200 OK` on `/health`). If the web application stops responding, the ASG marks the instance `Unhealthy`, terminates it, and launches a fresh instance from the Launch Template.

* **Launch Templates over Legacy Launch Configurations:**
  * Using Launch Templates (instead of deprecated Launch Configurations) allows versioning configuration states, combining On-Demand and Spot instances within a single ASG, and dynamically injecting User Data scripts for automated bootstrap installations.