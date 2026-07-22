---
title : "CloudWatch + SNS — Monitoring"
date : 2026-07-12
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

#### Luồng dữ liệu

```
EC2/ALB/RDS/SQS metrics ──► CloudWatch Alarm ──► SNS Topic ──► Email
Log container ứng dụng ──► CloudWatch Logs (log group /fashion-shop/prod/backend)
```

Hệ thống được thiết lập với 5 alarm: EC2 CPU (2 alarm, 1 cho mỗi instance) > 80%, ALB 5xx > 10/phút, RDS connections > 80, và SQS DLQ có message.

#### 1. Xem log ứng dụng

Để kiểm tra việc ghi log thời gian thực:
1. Trên **CloudWatch console**, vào mục **Logs** → **Log groups** và chọn `/fashion-shop/prod/backend`.
2. Nhấp vào **Live Tail**, chọn log group rồi bắt đầu stream.
3. Mở storefront và tương tác với trang, các dòng log mới liên tục xuất hiện trên màn hình theo thời gian thực.

![Log](/images/5-Workshop/5.7-CloudWatch-SNS/live.png)

#### 2. Xem SNS Topic + trạng thái subscription

Để đảm bảo luồng cảnh báo hoạt động:
1. Tại **SNS console**, vào **Topics** và mở `fashion-shop-prod-alerts`.
2. Ở tab **Subscriptions**, status hiện tại là `Confirmed` (chứ không phải `Pending confirmation`).

![Topic](/images/5-Workshop/5.7-CloudWatch-SNS/confirm.png)

#### 3. Xem danh sách 5 Alarm

1. Trên **CloudWatch console**, chuyển sang **Alarms** → **All alarms**.
2. Lọc danh sách theo tên bắt đầu bằng `fashion-shop-prod` — cả 5 alarm đều đang ở trạng thái xanh **OK**.

![Alarm](/images/5-Workshop/5.7-CloudWatch-SNS/alarm.png)

#### 4. Test thật — tự tạo sự cố giả để nhận email cảnh báo

Để kiểm tra xem hệ thống cảnh báo có phản ứng đúng không, cách an toàn nhất là thả một message trực tiếp vào dead-letter queue. Quá trình này hoàn toàn thao tác trên Console:

1. Mở **SQS console** và chọn queue `fashion-shop-prod-order-dlq`.
2. Chọn **Send and receive messages**, trong mục **Message body** nhập một nội dung bất kỳ (vd: `{"test":"trigger-alarm"}`) và nhấn **Send message**.
3. Vì CloudWatch kiểm tra mỗi 5 phút, sau khoảng 5-10 phút hộp mail nhận được một email mang tiêu đề `ALARM: "fashion-shop-prod-order-dlq-has-messages"`. Trên CloudWatch Console, alarm này cũng đã chuyển sang màu đỏ **In alarm**.

![Trigger alarm](/images/5-Workshop/5.7-CloudWatch-SNS/inalarm.png)

**Dọn dẹp sau khi test:** 
Quay lại trang DLQ, chọn **Actions → Purge** để làm rỗng queue. Sau chu kỳ ~5 phút tiếp theo, alarm tự động phục hồi về màu xanh **OK**.

![Recovery](/images/5-Workshop/5.7-CloudWatch-SNS/outalarm.png)
