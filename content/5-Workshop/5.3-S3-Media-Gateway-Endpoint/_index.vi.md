---
title : "S3 Media + Gateway Endpoint"
date : 2026-07-12
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Luồng dữ liệu

```
Admin FE ──► POST /api/v1/admin/products/{id}/images/upload
                │
                ▼ (qua S3 Gateway Endpoint, không ra internet)
           EC2 Spring Boot ──► S3 Media Bucket
                                    │
                                    ▼
                        CloudFront /media/* ──► User xem ảnh
```

#### 1. Kiểm tra S3 VPC Gateway Endpoint

Để đảm bảo kết nối nội bộ từ EC2 đến S3, Endpoint được kiểm tra:
1. Trên **VPC console**, chuyển đến mục **Endpoints**.
2. Endpoint có tên `fashion-shop-prod-s3-endpoint` với kiểu `Gateway` và service `com.amazonaws.ap-southeast-1.s3`.
3. Tại tab **Route tables**, xác nhận endpoint này đã được gắn cả route table public lẫn 2 route table private (do EC2 sử dụng route table private).

![Endpoint](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/endpoints.png)

#### 2. Kiểm tra S3 Media bucket

1. Trên **S3 console**, điều hướng tới bucket `fashion-shop-prod-media`.
2. Tại tab **Permissions**, mục **Block public access** xác nhận cả 4 tùy chọn đã được bật (bucket hoàn toàn private).
3. Tại phần **Bucket policy**, chính sách đã được thiết lập để chỉ cho phép principal `cloudfront.amazonaws.com` đọc dữ liệu, đi kèm điều kiện (Condition) khớp với ARN CloudFront distribution của Frontend.

![Bucket policy](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/policy.png)

#### 3. Kiểm tra CloudFront gắn thêm origin `/media/*`

1. Trên **CloudFront console**, mở distribution `E1RFSXKTT3PDNR`.
2. Tại tab **Origins**, cấu hình hiện tại có 3 origin: `s3-site` (FE build), `alb-backend` (API), và `s3-media` (ảnh).
3. Tại tab **Behaviors**, path pattern `/media/*` đã được trỏ thành công tới origin `s3-media` với cache policy là `CachingOptimized`.

![Origin](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/origin.png)
![Behaviors](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/behaviors.png)

#### 4. Test end-to-end: upload ảnh qua trang Admin

Để kiểm chứng tính năng upload, test được thực hiện trực tiếp:
1. Truy cập vào `https://d1tz40a6c8kj4v.cloudfront.net` và đăng nhập với tài khoản `admin@shop.com`.
2. Vào phần **Quản trị → Sản phẩm**, chọn sửa 1 sản phẩm bất kỳ và nhấn **+ Tải ảnh lên**.
3. Chọn một bức ảnh và chờ quá trình upload hoàn tất. Ngay lập tức, ảnh xuất hiện trong modal với URL có dạng: `https://d1tz40a6c8kj4v.cloudfront.net/media/products/{id}/{uuid}.jpg`.

Ảnh cũng hiển thị thành công ngoài storefront dành cho người dùng.

![URL_anhup](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/URL_anhup.png)

#### 5. Đối chiếu: object thật trong S3

1. Quay lại **S3 console** → bucket `fashion-shop-prod-media` → điều hướng theo đường dẫn thư mục `media/products/{id}/`.
2. File `.jpg`/`.png` vừa upload ở bước 4 nằm gọn trong bucket với key trùng khớp với URL.

![URL_bucket](/images/5-Workshop/5.3-S3-Media-Gateway-Endpoint/url_bucket.png)