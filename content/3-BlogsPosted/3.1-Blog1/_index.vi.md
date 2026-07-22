---
title: 'Workload Discovery on AWS – Công cụ "vẽ" kiến trúc đám mây siêu thông minh!'
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

Chào mọi người, mình là sinh viên đang thực tập tại AWS. Trong quá trình làm việc, mình có cơ hội tìm hiểu sâu về **Workload Discovery on AWS** (trước đây gọi là AWS Perspective) – một giải pháp cực kỳ hữu ích để visualize workload trên AWS. Hôm nay mình xin chia sẻ lại những kiến thức cốt lõi về kiến trúc của nó nhé.

### Workload Discovery on AWS là gì?
Đây là công cụ giúp bạn:
* **Tự động thu thập inventory** tài nguyên AWS từ nhiều Account & Region.
* **Vẽ biểu đồ kiến trúc (architecture diagram)** chi tiết dựa trên dữ liệu thực tế (live data).
* **Hiểu rõ mối quan hệ** giữa các tài nguyên (relationships).
* **Xây dựng, tùy chỉnh và chia sẻ** diagram một cách dễ dàng.
* **Hỗ trợ cả cost analysis** để xem chi phí của các resource.

---

### Kiến trúc tổng quan (Architecture Overview)
Khi deploy solution này bằng CloudFormation (mặc định), nó sẽ tạo ra một môi trường với các thành phần chính sau:

#### 1. Web UI (Giao diện người dùng)
* Host trên Amazon S3 + phân phối qua Amazon CloudFront.
* Bảo mật bằng Amazon Cognito (xác thực người dùng).
* Được bảo vệ bởi AWS WAF.

#### 2. Backend & API
* Sử dụng AWS AppSync làm GraphQL API chính.
* Xác thực bằng JWT từ Cognito.

#### 3. Data Layer
* **Amazon Neptune (Graph Database)**: Lưu trữ mối quan hệ giữa các tài nguyên.
* **Amazon OpenSearch Service**: Hỗ trợ tìm kiếm mạnh mẽ.
* **Amazon DynamoDB**: Lưu cấu hình và settings.

#### 4. Discovery Component (Phần cốt lõi)
* Chạy trên AWS Fargate (ECS) với lịch cron mỗi 15 phút.
* Sử dụng AWS Config + AWS SDK để quét inventory.
* Lưu dữ liệu vào Neptune & OpenSearch qua AppSync.

#### 5. Cost Analysis
* Dùng Amazon Athena query AWS Cost and Usage Report (CUR).
* Giúp xem chi phí ước tính của các resource trong diagram.

> Toàn bộ architecture được thiết kế serverless-friendly, an toàn và scalable.

![Workload Discovery on AWS Architecture](/images/3-BlogsPosted/image1.png)

---

### Lý do mình rất recommend tool này
* Giúp nhanh chóng hiểu bức tranh tổng thể của hệ thống mà không cần vẽ tay.
* Diagram tương tác: kéo thả, zoom, group theo VPC, AZ, Account...
* Luôn cập nhật gần real-time.
* Có thể export/share diagram đẹp cho team hoặc khách hàng.

Nếu bạn đang quản lý hạ tầng AWS, mình cực kỳ recommend thử solution này!

* **Tài liệu chính thức:** [Architecture Overview Workload Discovery on AWS](https://docs.aws.amazon.com/solutions/latest/workload-discovery-on-aws/architecture-overview.html)