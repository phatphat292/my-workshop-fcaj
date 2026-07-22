---
title: "Week 7 Worklog"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---



### Week 7 Objectives:

* Master Global Content Delivery Networks (CDN) and edge compute mechanisms using Amazon CloudFront to reduce latency and protect origin servers.
* Implement Origin Access Control (OAC) and custom SSL/TLS certificates via AWS Certificate Manager (ACM) to enforce end-to-end security.
* Analyze project functional and non-functional requirements to initiate the preliminary AWS cloud architecture draft for the final project.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Studied Amazon CloudFront CDN architecture: Edge Locations, Regional Edge Caches, Cache Behaviors, TTLs, and Origin Types (S3, ALB, Custom HTTP) | 01/06/2026 | 01/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Researched Edge Computing paradigms (CloudFront Functions vs. Lambda@Edge) and private content delivery using Signed URLs/Signed Cookies and ACM certificates | 02/06/2026 | 02/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - **Hands-on CloudFront & OAC:** Created a CloudFront distribution, configured Origin Access Control (OAC) to secure an S3 origin, and tested global edge caching and cache invalidation | 03/06/2026 | 03/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Analyzed project requirements: Identified key business domains, high availability mandates, throughput expectations, and security compliance constraints | 04/06/2026 | 04/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Hands-on Architecture Sketching:** Sketched the initial multi-AZ 3-tier AWS architecture diagram integrating VPC, ALB, EC2 Auto Scaling, RDS MySQL, S3, and CloudFront | 05/06/2026 | 05/06/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Week 7 Insights & Reflections:

* **Global Edge Content Acceleration & Origin Shielding via Amazon CloudFront:**
  * **Reducing Time-To-First-Byte (TTFB):** CloudFront caches static media (images, CSS/JS) and dynamic assets across hundreds of global Edge Locations. Users fetch content from the nearest Edge point over AWS's optimized backbone network rather than sending requests all the way back to origin servers in remote AWS regions.
  * **Origin Offloading:** Edge caching absorbs up to 80-90% of incoming GET traffic. This drastically shields backend EC2 compute instances and S3 origins from traffic spikes, lowering bandwidth costs and reducing origin CPU utilization.

* **Security Boundaries: Origin Access Control (OAC) vs. Legacy OAI:**
  * **Eliminating Public S3 Buckets:** Exposing S3 buckets directly to the public internet poses severe security risks.
  * **Why OAC is the Standard:** Origin Access Control (OAC) allows S3 bucket policies to restrict access strictly to requests signed by designated CloudFront distributions (`AWS:SourceArn`). OAC supports modern security standards including AWS KMS server-side encryption, HTTP `PUT/POST` requests, and granular IAM conditions, completely superseding legacy Origin Access Identity (OAI).

* **Edge Compute Paradigms: CloudFront Functions vs. Lambda@Edge:**
  * **CloudFront Functions:** Lightweight JavaScript functions executing in sub-milliseconds directly at 600+ Edge Locations. Ideal for high-scale, low-complexity tasks like URL redirects, header manipulation, and simple JWT token validation.
  * **Lambda@Edge:** Full Node.js or Python runtime executing in Regional Edge Caches. Best suited for complex business logic, network calls to remote APIs, or heavy request/response body transformations before hitting origin servers.

* **Translating Requirements into Preliminary Cloud Architecture Blueprints:**
  * **Balancing the AWS Well-Architected Pillars:** Designing initial architecture requires managing trade-offs between Reliability (Multi-AZ deployment across subnets), Security (private subnets for DBs, public edge for CDN), and Performance Efficiency.
  * **Foundational 3-Tier Layout:** Laying out clear tier separation—Public Edge/Web Tier (CloudFront + ALB), Application Tier (Auto Scaling EC2/Containers), and Database Tier (RDS Multi-AZ)—establishes a clean blueprint that ensures isolation and decoupled scalability from day one.