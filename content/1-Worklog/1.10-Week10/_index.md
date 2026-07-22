---
title: "Week 10 Worklog"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:
* Consolidate and finalize the comprehensive master AWS architecture blueprint for the overall project workload.
* Collaborate closely with team members to trace end-to-end request flows, evaluate system execution paths, and identify architectural bottlenecks impacting user experience.
* Propose actionable UI/UX design enhancements that align frontend interactions with backend cloud execution capabilities (asynchronous feedback, latency masking, and real-time status tracking).

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Consolidated all subsystem diagrams (Edge, VPC, Compute, Database, Security, and Observability) into a unified master AWS architecture blueprint | 22/06/2026 | 22/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Conducted end-to-end architectural fault-tolerance reviews, simulating failure scenarios (AZ outages, cache misses, RDS failovers) across the consolidated blueprint | 23/06/2026 | 23/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - **Team Workflow Evaluation:** Collaborated with team members to map end-to-end user journeys from the client UI down to CloudFront, ALB, EC2, ElastiCache, and RDS | 24/06/2026 | 24/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Analyzed system execution bottlenecks and identified UI friction points caused by backend processing latency and blocking API calls | 25/06/2026 | 25/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **UI/UX Optimization Proposals:** Formulated and documented concrete UI/UX improvement strategies (loading skeletons, optimistic rendering, async progress feedback) and finalized project technical specifications | 26/06/2026 | 26/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Lessons Learned & Key Architectural Insights:

* **Consolidating the Master Blueprint: From Isolated Components to a Unified Cloud Ecosystem:**
  * **The Challenge of System Integration:** Designing cloud infrastructure in silos—focusing separately on VPC subnets, database read replicas, or WAF security rules—often masks hidden cross-tier friction. Finalizing the master architecture required verifying how every single component interacts as a unified whole. We ensured that data flowing from the edge (CloudFront/WAF) cleanly transitions through public subnets (ALB), private application subnets (Auto Scaling EC2), and isolated data subnets (RDS Multi-AZ and ElastiCache) without breaking security boundaries.
  * **Ensuring Structural Cohesion:** A key insight during master blueprint consolidation was simplifying routing topologies. Eliminating redundant cross-AZ pathways and ensuring explicit security group chaining (e.g., Database tier accepting traffic *only* from the Application Security Group) guaranteed that the final architecture remains both highly resilient and easy to audit.

* **Bridging Cloud Backend Latency with Frontend UI/UX Design:**
  * **Why Cloud Architecture Dictates User Experience:** Infrastructure performance directly impacts user perception. If an application performs heavy database writes or complex background calculations synchronously, the frontend UI inevitably freezes or hangs while waiting for an API response. Tracing request execution paths revealed that backend latency bottlenecks can easily be misperceived by users as "broken" software if the interface lacks appropriate visual feedback.
  * **Designing for Asynchronous Realities:** To mitigate latency friction, we proposed shifting heavy synchronous operations to asynchronous processing models. Instead of forcing users to wait for a blocking HTTP request, the UI should immediately provide optimistic updates or visual feedback (skeleton screens, step-by-step progress bars, and status polling). This bridges the gap between backend cloud execution and frontend visual feedback, dramatically improving perceived application speed.

* **Cross-Functional Team Collaboration & Architectural Synergy:**
  * **Breaking Down the Silo between Infra and App Teams:** Infrastructure engineers cannot design effective cloud blueprints in isolation from application developers. Collaborating directly with the team to walk through execution paths highlighted how frontend state management and backend API design depend heavily on cloud infrastructure behavior.
  * **Shared Ownership of System Execution:** Working together to evaluate user flows built a shared understanding across the team. Developers gained clarity on network topology constraints and caching behavior, while infrastructure planning was refined to directly support the application's true operational needs and user journeys.