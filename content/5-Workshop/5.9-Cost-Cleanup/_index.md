---
title : "Cost & Cleanup"
date : 2026-07-12
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

#### Estimated monthly cost (running 24/7)

| Service | Estimated cost |
|---|---|
| NAT Gateway x2 | ~$85-90 |
| ALB | ~$20 |
| EC2 (Auto Scaling Group, 2-4x t3.micro) | ~$20-40 |
| RDS (db.t3.micro, Multi-AZ) | ~$35 |
| ElastiCache Redis (cache.t3.micro) | ~$13-15 |
| WAF (Web ACL + 4 rules) | ~$9 |
| S3 + CloudFront + DynamoDB + SQS + Lambda + SES + SNS + CloudWatch | ~$2-3 |
| **Estimated total** | **~$184-212/month** |

{{% notice tip %}}
**EC2 cost is now a range, not a fixed number** — the Auto Scaling Group keeps a baseline of 2 instances (~$184-192/month total) and scales up to 4 under sustained CPU load (target-tracking at 60% average, up to ~$204-212/month total). The extra cost only applies for the hours the ASG is actually scaled out, not as a flat monthly addition.
{{% /notice %}}

{{% notice tip %}}
**NAT Gateway accounts for nearly half the bill** — the single largest and most easily overlooked cost. It could be cut by roughly $43/month by using 1 NAT Gateway instead of 2 (trade-off: losing AZ-level fault tolerance for the application's outbound internet traffic — an acceptable trade for a small project).
{{% /notice %}}



#### Cleanup — tearing down the system

Because the whole system was managed as Infrastructure-as-Code (IaC), removal at the end of the workshop followed the two definitions in order.

{{% notice warning %}}
Tearing down the infrastructure **permanently** deletes data. While RDS takes a final snapshot before deletion, S3 and DynamoDB are not backed up automatically. This step only runs once the system is definitively no longer needed.
{{% /notice %}}

1. The **backend infrastructure** came down first (fewer things depend on it) — VPC, EC2, ALB, RDS, and all the supporting services.
2. Then the **frontend infrastructure** — the S3 site bucket, CloudFront, and WAF.
3. Finally, a manual check confirmed nothing was left behind — these are commonly missed because they aren't part of the IaC definition:
   + **ECR** → repository `fashion-shop-backend` (holding the application image) — removed separately.
   + **CloudWatch Logs** → the log group can still hold old logs that incur storage costs, so it was deleted.
   + **S3** → the versioned site bucket needs all object versions permanently deleted before the bucket itself can be removed.


