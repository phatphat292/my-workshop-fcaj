---
title : "The deployed environment"
date : 2026-07-12
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### At a glance

The whole system is already deployed and running. In the chapters that follow, each service is inspected and tested directly through the AWS Console.

| Item | Value |
|---|---|
| Region | `ap-southeast-1` (Singapore) |
| Storefront URL | `https://d1tz40a6c8kj4v.cloudfront.net` |
| Admin login (for testing) | `admin@shop.com` |

Every resource is named with a consistent prefix — `fashion-shop-prod-*` for the backend services and `maison-edition-prod-*` for the frontend/edge services — making each one easy to find by typing the prefix into the search box on each service's console page.

#### Resource name reference

Resource name reference used throughout the chapters:

| Service | Name |
|---|---|
| CloudFront distribution | `E1RFSXKTT3PDNR` |
| S3 media bucket | `fashion-shop-prod-media` |
| ElastiCache cluster | `fashion-shop-prod-redis` |
| Order queue / DLQ | `fashion-shop-prod-order-created` / `fashion-shop-prod-order-dlq` |
| Lambda function | `fashion-shop-prod-order-processor` |
| DynamoDB table | `fashion-shop-prod-activity-log` |
| SNS alert topic | `fashion-shop-prod-alerts` |
| Backend log group | `/fashion-shop/prod/backend` |
| Web ACL (WAF) | `maison-edition-prod-waf` |
