---
title: "Week 1 Worklog"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---



### Week 1 Objectives:

* Set up a proper AWS sandbox environment with promotional credits to practice without worrying about unexpected costs.
* Understand how AWS isolates cloud resources at the network level using VPCs, subnets, Internet Gateways, and Route Tables.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Onboarded to the First Cloud AI Journey program <br> - Created an AWS account and claimed the initial $100 credit to build a safe environment for labs | 20/04/2026 | 20/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Completed 5 AWS discovery tasks on Skill Builder <br> - Earned an additional $100 credit while getting used to navigating the AWS Console | 21/04/2026 | 21/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Researched VPC (Virtual Private Cloud) architecture <br> - Learned how CIDR blocks define IP address ranges and partition private cloud space | 22/04/2026 | 22/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Analyzed the difference between Public and Private subnets <br> - Explored how Internet Gateways (IGW) act as the entry and exit point for public traffic | 23/04/2026 | 23/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Learned how Route Tables direct traffic between subnets and external networks <br> - **Hands-on Lab:** Built a custom VPC with public/private subnets, attached an IGW, and configured route entries | 24/04/2026 | 24/04/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Week 1 Insights & Reflections:

* **Why AWS Sandbox Setup Matters:** Claiming the $100 credits right at the start gave me a stress-free environment to launch and test resources without worrying about accidental billing spikes.
* **Understanding Cloud Isolation:** I realized that a VPC is essentially our own virtual data center inside AWS. It keeps resources isolated from other AWS tenants unless we explicitly route them out.
* **What Actually Makes a Subnet "Public":** I used to think a public subnet had a special setting, but I learned that a subnet is only "public" if its associated Route Table has an explicit route (`0.0.0.0/0`) pointing to an Internet Gateway (IGW). Without that route, traffic stays strictly inside the VPC.
* **Security Mindset:** Separating workload instances into public subnets (web facing) and private subnets (databases) is the first step in applying defense-in-depth security on AWS.