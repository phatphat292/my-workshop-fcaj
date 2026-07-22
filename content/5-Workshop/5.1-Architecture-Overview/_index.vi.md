---
title : "Tổng quan kiến trúc"
date : 2026-07-12
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Hai lớp, một origin duy nhất

Hệ thống được thiết kế gồm hai lớp:

+ **Lớp giao diện** — bản build React tĩnh phục vụ từ S3, phân phối qua
  CloudFront (CDN + HTTPS) và được WAF bảo vệ.
+ **Lớp xử lý** — ứng dụng Spring Boot chạy trên EC2 (trong Auto Scaling
  Group, 2-4 instance) sau Application Load Balancer, dùng RDS PostgreSQL làm
  cơ sở dữ liệu chính, cùng toàn bộ các dịch vụ hỗ trợ (S3 Media, ElastiCache,
  SQS/Lambda, DynamoDB, CloudWatch/SNS).

CloudFront định tuyến đường dẫn `/api/*` thẳng sang ALB của lớp xử lý. Nhờ vậy trình duyệt luôn chỉ giao tiếp với một origin duy nhất (`d1tz40a6c8kj4v.cloudfront.net`) — giao diện và phần xử lý không bao giờ dính lỗi CORS dù chạy trên các dịch vụ hoàn toàn tách biệt.

Mô hình tổng thể như sau:
![mohinh](/images/5-Workshop/5.1-Architecture-Overview/mohinh.png)

#### Cấu hình mạng

Lớp mạng được cấu hình với:
+ 1 VPC trải trên 2 Availability Zone (`ap-southeast-1a`, `ap-southeast-1b`).
+ 2 public subnet (ALB + NAT Gateway), 2 private subnet (EC2 + RDS).
+ 2 NAT Gateway (1 mỗi AZ) — khoản chi phí lớn nhất trong toàn hệ thống, chi tiết được ghi chú ở chương [Chi phí](../5.9-Cost-Cleanup/).
+ 1 **S3 Gateway VPC Endpoint** — cho phép EC2 truy cập S3 mà không cần đi qua NAT.

**Kiểm tra Resource Map và EC2 Instances:**
- Trên VPC console, ở phần **Resource map**, toàn bộ subnet, route table và gateway đều được liên kết đúng.
![VPC](/images/5-Workshop/5.1-Architecture-Overview/VPC.png)
- Trên EC2 console, mục **Auto Scaling Groups**, `fashion-shop-prod-asg` cho thấy 2 instance đang `InService` (min 2 / desired 2 / max 4, target-tracking theo CPU trung bình 60%), cả 2 đều là target `healthy` trên ALB.
![ALB](/images/5-Workshop/5.1-Architecture-Overview/ALB.png)

#### Danh sách dịch vụ đang chạy

| Nhóm | Dịch vụ | Vai trò |
|---|---|---|
| Compute | EC2 (Auto Scaling Group, 2-4), ALB | Chạy & cân bằng tải ứng dụng, tự scale theo CPU |
| Giao diện | S3, CloudFront, WAF | Phục vụ storefront + CDN + chặn tấn công |
| Dữ liệu | RDS PostgreSQL, ElastiCache Redis, DynamoDB | DB nghiệp vụ / cache / activity log |
| Async | SQS, Lambda, SES | Xử lý đơn hàng chạy ngầm, gửi email |
| Giám sát | CloudWatch, SNS | Log, alarm, cảnh báo qua email |