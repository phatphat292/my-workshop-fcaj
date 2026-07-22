---
title : "S3 Media + Gateway Endpoint"
date : 2026-07-12
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Data Flow

```
Admin FE ──► POST /api/v1/admin/products/{id}/images/upload
                │
                ▼ (via S3 Gateway Endpoint, no internet egress)
           EC2 Spring Boot ──► S3 Media Bucket
                                    │
                                    ▼
                        CloudFront /media/* ──► User views the image
```

#### 1. Inspect the S3 VPC Gateway Endpoint

To ensure secure internal connections from EC2 to S3, the Endpoint was checked:
1. On the **VPC console**, opened **Endpoints**.
2. The endpoint named `fashion-shop-prod-s3-endpoint` — type `Gateway`, service `com.amazonaws.ap-southeast-1.s3`.
3. In the **Route tables** tab, confirmed this endpoint was attached to both the public route table and the 2 private route tables (since the EC2 instances use the private ones).

![Endpoint](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/endpoints.png)

#### 2. Inspect the S3 Media bucket

1. On the **S3 console**, located the bucket `fashion-shop-prod-media`.
2. In the **Permissions** tab, under **Block public access**, all 4 checkboxes were enabled (making the bucket fully private).
3. Under the **Bucket policy**, the configuration correctly allowed only the `cloudfront.amazonaws.com` principal, with a condition matching the exact ARN of the Frontend CloudFront distribution.

![Bucket policy](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/policy.png)

#### 3. Inspect the new `/media/*` CloudFront origin

1. On the **CloudFront console**, opened the distribution `E1RFSXKTT3PDNR`.
2. In the **Origins** tab, 3 origins configured: `s3-site` (FE build), `alb-backend` (API), and `s3-media` (images).
3. In the **Behaviors** tab, the path pattern `/media/*` correctly pointed to the `s3-media` origin, utilizing the `CachingOptimized` cache policy.

![Origin](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/origin.png)
![Behaviors](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/behaviors.png)

#### 4. End-to-end test: upload an image via the Admin page

To validate the upload feature end-to-end:
1. Signed in at `https://d1tz40a6c8kj4v.cloudfront.net` as `admin@shop.com`.
2. Under **Admin → Products**, edited a product and clicked **+ Upload image**.
3. Picked an image, waited for the upload to finish, and saw the image appear immediately in the modal with a URL like:
   `https://d1tz40a6c8kj4v.cloudfront.net/media/products/{id}/{uuid}.jpg`.

The storefront product page also successfully rendered the newly uploaded image.

![URL_anhup](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/URL_anhup.png)

#### 5. Cross-check: the real object in S3

1. Back in the **S3 console** → bucket `fashion-shop-prod-media` → navigated to the path `media/products/{id}/`.
2. Found the `.jpg`/`.png` file just uploaded, with a key matching the URL from the previous step.

![URL_bucket](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/url_bucket.png)
