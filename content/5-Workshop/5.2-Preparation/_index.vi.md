---
title : "Môi trường đã triển khai"
date : 2026-07-12
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### Nhìn nhanh

Toàn bộ hệ thống đã được triển khai và đang chạy. Trong các phần tiếp theo, từng dịch vụ được xem và kiểm tra ngay trên AWS Console.

| Mục | Giá trị |
|---|---|
| Region | `ap-southeast-1` (Singapore) |
| URL storefront | `https://d1tz40a6c8kj4v.cloudfront.net` |
| Tài khoản admin (để test) | `admin@shop.com` |

Mọi resource đều được đặt tên theo cùng một tiền tố — `fashion-shop-prod-*` cho các dịch vụ phía xử lý và `maison-edition-prod-*` cho các dịch vụ giao diện/lớp ngoài — giúp dễ dàng tìm kiếm trên trang console của từng dịch vụ.

#### Bảng tra tên resource

Bảng tra cứu tên resource dùng xuyên suốt quá trình kiểm tra:

| Dịch vụ | Tên |
|---|---|
| CloudFront distribution | `E1RFSXKTT3PDNR` |
| S3 media bucket | `fashion-shop-prod-media` |
| ElastiCache cluster | `fashion-shop-prod-redis` |
| Queue đơn hàng / DLQ | `fashion-shop-prod-order-created` / `fashion-shop-prod-order-dlq` |
| Lambda function | `fashion-shop-prod-order-processor` |
| DynamoDB table | `fashion-shop-prod-activity-log` |
| SNS topic cảnh báo | `fashion-shop-prod-alerts` |
| Log group phía xử lý | `/fashion-shop/prod/backend` |
| Web ACL (WAF) | `maison-edition-prod-waf` |
