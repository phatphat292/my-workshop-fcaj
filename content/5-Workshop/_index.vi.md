---
title: "Workshop"
date: 2026-07-12
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

#### Tổng quan

Tài liệu này là nhật ký (worklog) ghi chép lại quá trình triển khai kiến trúc AWS cho **Maison Édition** — một storefront thời trang. Phần giao diện được phục vụ từ S3 + CloudFront, phần xử lý chạy trên EC2 sau Application Load Balancer với cơ sở dữ liệu RDS PostgreSQL. Ngoài phần nền tảng đó, hệ thống bổ sung sáu chức năng, mỗi chức năng giải quyết một khoảng trống cụ thể: S3 Media (nơi lưu và phục vụ ảnh sản phẩm riêng, thay vì để app server xử lý), cache Redis (giảm tải đọc lên RDS cho dữ liệu catalog truy cập nhiều), xử lý đơn hàng bất đồng bộ với SQS + Lambda (giữ luồng checkout phản hồi nhanh, không phải chờ gửi email/ghi log), ghi lịch sử hoạt động trên DynamoDB (audit trail thao tác người dùng), giám sát bằng CloudWatch + SNS (cảnh báo sớm khi có sự cố), và tường lửa WAF ở lớp ngoài cùng (chặn tấn công trước khi chạm tới ứng dụng).

{{% notice tip %}}
**🔗 Trải nghiệm ứng dụng thực tế tại:** [https://d1tz40a6c8kj4v.cloudfront.net/](https://d1tz40a6c8kj4v.cloudfront.net/)
{{% /notice %}}

**▶️ Video demo:**
{{< youtube oFeH611LTfE >}}

Mỗi chương ghi nhận vai trò của từng dịch vụ và cách dịch vụ đó được cấu hình, kiểm tra trực tiếp trên AWS Console.

```
User Request
    │
    ▼
AWS WAF ──── Chặn SQLi/XSS, rate limit 2000 req/5min/IP
    │
    ▼
AWS CloudFront
    ├── /api/*   ──────────────────────────────► ALB (HTTP:80) ──► EC2 x2 (Spring Boot)
    ├── /media/* ──► S3 Media Bucket (ảnh sản phẩm)                    │
    └── /*       ──► S3 FE Bucket (React build)                        ├──► RDS PostgreSQL
                                                                       ├──► ElastiCache Redis (cache)
                                                                       ├──► S3 (upload ảnh, qua Gateway Endpoint)
                                                                       └──► SQS ──► Lambda ──► SES (email) + DynamoDB (activity log)

CloudWatch (metrics + logs) ──► SNS ──► Email cảnh báo
```

#### Nội dung

1. [Tổng quan kiến trúc](5.1-Architecture-Overview/)
2. [Chuẩn bị](5.2-Preparation/)
3. [S3 Media + Gateway Endpoint](5.3-S3-Media-Gateway-Endpoint/)
4. [ElastiCache Redis](5.4-ElastiCache-Redis/)
5. [SQS + Lambda — xử lý đơn hàng](5.5-SQS-Lambda-Order/)
6. [DynamoDB — Activity Log](5.6-DynamoDB-Activity-Log/)
7. [CloudWatch + SNS — Monitoring](5.7-CloudWatch-SNS/)
8. [WAF](5.8-WAF/)
9. [Chi phí & Cleanup](5.9-Cost-Cleanup/)
