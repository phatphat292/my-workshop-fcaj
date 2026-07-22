---
title : "SQS + Lambda — xử lý đơn hàng"
date : 2026-07-12
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Luồng dữ liệu

```
Khách đặt hàng ──► EC2 lưu đơn vào RDS ──► publish message vào SQS ──► trả lời khách ngay
                                                        │
                                                        ▼ (tự động trigger)
                                                 Lambda order-processor
                                                        │
                                                        ├──► SES: gửi email xác nhận
                                                        └──► DynamoDB: ghi log ORDER_CREATED

Gửi lỗi 3 lần liên tiếp ──► message rơi vào Dead Letter Queue (DLQ)
```

#### 1. Kiểm tra SQS Queue chính

Để đảm bảo hệ thống nhận đơn hàng không đồng bộ hoạt động tốt, cấu hình queue được kiểm tra:
1. Trên **SQS console**, mở queue `fashion-shop-prod-order-created`.
2. Tại tab **Dead-letter queue**, xác nhận queue này đã được cấu hình trỏ tới `fashion-shop-prod-order-dlq` với thông số `Maximum receives = 3`.

![Queue](/images/5-Workshop/5.5-SQS-Lambda-Order/queue.png)

#### 2. Kiểm tra Lambda function

1. Trên **Lambda console**, mở function `fashion-shop-prod-order-processor`.
2. Tại tab **Configuration → Triggers**, trigger chính là SQS queue `order-created` với cấu hình batch size là 10.
3. Tại tab **Configuration → Environment variables**, các biến môi trường cần thiết như `SES_SENDER`, `SHOP_NAME`, và `ACTIVITY_LOG_TABLE` đều đã được thiết lập đúng.

![Trigger](/images/5-Workshop/5.5-SQS-Lambda-Order/trigger.png)
![Environment](/images/5-Workshop/5.5-SQS-Lambda-Order/envi.png)

#### 3. Kiểm tra SES đã verify email gửi

1. Trên **SES console** (tại đúng region `ap-southeast-1`), vào mục **Identities**.
2. Địa chỉ email cấu hình trong `ses_sender_email` đã có trạng thái **Verified**.

![SES](/images/5-Workshop/5.5-SQS-Lambda-Order/ses.png)

#### 4. Test thật — đặt 1 đơn hàng

Để xác thực quy trình end-to-end, tiến hành đặt hàng:
1. Đăng nhập bằng tài khoản có email trùng với `ses_sender_email` (do SES đang ở chế độ sandbox nên **cả người gửi lẫn người nhận đều phải verified**).
2. Thêm sản phẩm vào giỏ → chọn **Thanh toán** → đặt hàng.
3. Trong vòng 10-30 giây, hộp mail nhận được email với tiêu đề "Xác nhận đơn hàng ODxxxxx".

![Success](/images/5-Workshop/5.5-SQS-Lambda-Order/success.png)

#### 5. Đối chiếu log Lambda

1. Tại **Lambda console** → function → tab **Monitor** → **View CloudWatch logs**.
2. Log stream mới nhất có các dòng `Processing order ODxxxxx` cùng với `Email sent for order ODxxxxx`. Điều này chứng tỏ Lambda đã được trigger thành công và chạy mượt mà.

![Log](/images/5-Workshop/5.5-SQS-Lambda-Order/log.png)

{{% notice tip %}}
Để test cơ chế lỗi/DLQ mà không cần đặt đơn thật, một message giả được bơm vào DLQ như mô tả ở chương [CloudWatch + SNS](../5.7-CloudWatch-SNS/) — cách đó cũng dùng để kiểm tra alarm `order-dlq-has-messages`.
{{% /notice %}}
