---
title: "Week 2 Worklog"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---



### Week 2 Objectives:

* Master identity and access management (IAM) to enforce the Principle of Least Privilege across the team.
* Deep dive into custom network setup (VPC) and understand instance isolation.
* Learn how to properly provision, secure, and manage Amazon EC2 virtual servers inside a custom VPC.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Studied Identity and Access Management (IAM) fundamentals <br> - Learned about IAM Users, Groups, Roles, Policies, and Multi-Factor Authentication (MFA) | 27/04/2026 | 27/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Hands-on IAM:** Configured IAM Users for team members, set up custom JSON policies, and enabled MFA to lock down the Root account | 28/04/2026 | 28/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Researched VPC security layers: Security Groups (stateful) vs Network ACLs (stateless) <br> - Designed a custom multi-subnet VPC network layout | 29/04/2026 | 29/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Explored EC2 instance families, AMI selection, Key Pairs, and EBS storage options | 30/04/2026 | 30/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Hands-on EC2:** Provisioned an EC2 instance inside the custom VPC, configured Security Group rules (SSH/HTTP), and connected via SSH using Key Pairs | 01/05/2026 | 01/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Week 2 Insights & Reflections:

* **Security Mindset & The Principle of Least Privilege in IAM:**
  * **Root Locking Philosophy:** I came to appreciate why the AWS Root account should be locked away behind MFA immediately after creation. Everyday operations must be executed via dedicated IAM Users or Federated Roles with explicit, scoped permissions.
  * **Static Credentials vs. IAM Roles:** Storing access key pairs (`AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`) locally or hardcoding them into configuration files introduces significant security risks. Instead, leveraging IAM Roles—which issue temporary, self-rotating credentials—is the industry standard for granting applications and EC2 instances access to other AWS services.

* **Packet Filtering Mechanics: Security Groups vs. Network ACLs:**
  * **Stateful Filtering at ENI (Security Group):** A Security Group operates at the Virtual Network Interface (ENI) level. Because it is *stateful*, once an inbound packet is allowed (e.g., port 80), the hypervisor automatically tracks the connection state and permits the return outbound response regardless of outbound rules.
  * **Stateless Filtering at Subnet Boundary (NACL):** Network ACLs guard the entire subnet perimeter and operate in a *stateless* manner. Every packet is evaluated independently against ordered rule numbers. For a request to succeed through a NACL, both inbound rules (e.g., allow port 443) and outbound rules (allowing ephemeral ports `1024-65535` for client responses) must be explicitly configured.

* **Systematic Troubleshooting Methodology for EC2 Connection Issues:**
  * When an SSH connection (`ssh -i key.pem ec2-user@<IP>`) hangs or throws a `Connection Timed Out` error, it indicates a network routing/filtering blocker rather than an OS-level issue.
  * I developed a step-by-step diagnostic order:
    1. **Public IP & Subnet:** Ensure the instance resides in a public subnet and holds an assigned Elastic/Public IP.
    2. **Route Table:** Verify `0.0.0.0/0` is routed to an active Internet Gateway (IGW).
    3. **Security Group:** Confirm port 22 is open for inbound traffic from the client IP.
    4. **Network ACL:** Check that subnet NACL rules allow inbound port 22 and outbound ephemeral ports.
    5. **Key Permission:** Verify local private key file permissions are strict (`chmod 400 key.pem`) to satisfy OpenSSH client requirements.

* **EBS Storage Decoupling & Persistence:**
  * Understanding that Amazon Elastic Block Store (EBS) is a network-attached storage (NAS/SAN) volume rather than a physical local disk attached to the physical hypervisor host was a key realization.
  * This architecture decouples compute from storage: if an EC2 instance fails or is stopped, the underlying EBS volume remains intact, enabling quick re-attachment to a healthy instance without data loss.