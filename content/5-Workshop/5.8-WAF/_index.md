---
title : "WAF"
date : 2026-07-12
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---

#### Data Flow

```
User Request
    │
    ▼
AWS WAF (attached to CloudFront)
    ├── AWSManagedRulesCommonRuleSet         — XSS, abnormal size, LFI...
    ├── AWSManagedRulesSQLiRuleSet           — SQL injection
    ├── AWSManagedRulesKnownBadInputsRuleSet — log4j, path traversal...
    ├── RateLimit — blocks an IP sending > 2000 requests/5 min
    └── PASS ──► CloudFront ──► ALB / S3
```

{{% notice warning %}}
A Web ACL for CloudFront **must be declared in the us-east-1 region** (even though CloudFront itself is a global service). The region on the Console always has to be switched before looking for the Web ACL.
{{% /notice %}}

#### 1. Inspect the Web ACL

To verify the security configurations:
1. Switched the Console region to **US East (N. Virginia) — us-east-1**.
2. Opened the **WAF & Shield console** → **Web ACLs** and selected `maison-edition-prod-waf`.
3. In the **Rules** tab, all 4 rules were present and correctly ordered by priority: `AWS-CommonRuleSet`, `AWS-SQLiRuleSet`, `AWS-KnownBadInputs`, and `RateLimit`.

![Rules](/images/5-Workshop/5.8-WAF/rule.png)

#### 2. Confirm WAF is attached to the right CloudFront distribution

1. Navigated to the **Associated AWS resources** tab on the Web ACL page.
2. The distribution `E1RFSXKTT3PDNR` (the FE storefront) was found attached.

![Associated AWS resources](/images/5-Workshop/5.8-WAF/associated.png)

#### 3. Test blocking XSS/SQLi

To ensure WAF actively blocks malicious traffic, test requests were run (using Git Bash or WSL to avoid PowerShell quoting bugs):

```bash
echo "--- normal request ---"
curl -s -o /dev/null -w 'HTTP=%{http_code}\n' 'https://d1tz40a6c8kj4v.cloudfront.net/api/v1/products?page=0'

echo "--- SQLi pattern ---"
curl -s -o /dev/null -w 'HTTP=%{http_code}\n' "https://d1tz40a6c8kj4v.cloudfront.net/api/v1/products?keyword=%27%20OR%20%271%27%3D%271"

echo "--- XSS pattern ---"
curl -s -o /dev/null -w 'HTTP=%{http_code}\n' "https://d1tz40a6c8kj4v.cloudfront.net/api/v1/products?keyword=%3Cscript%3Ealert(1)%3C%2Fscript%3E"
```

The output matched our expectations: the normal request returned HTTP status `200`, while the two malicious patterns returned `403` (effectively blocked by WAF at the edge).

![Curl command results](/images/5-Workshop/5.8-WAF/curl.png)

#### 4. Inspect the blocked requests in detail (Sampled requests)

To view the logs of the blocked requests:
1. Back on the Web ACL page, opened the **Sampled requests** tab.
2. Waited a few minutes after the test in step 3, then refreshed the page.
3. The requests just sent showed up marked with **Action = BLOCK** and the name of the rule that blocked them (either `AWS-SQLiRuleSet` or `AWS-CommonRuleSet`).

![Sampled requests](/images/5-Workshop/5.8-WAF/log.png)
