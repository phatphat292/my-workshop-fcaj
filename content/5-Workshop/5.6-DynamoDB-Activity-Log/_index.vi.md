---
title : "DynamoDB — Activity Log"
date : 2026-07-12
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Luồng dữ liệu

```
User xem sản phẩm / tìm kiếm ──► EC2 (@Async) ──► DynamoDB PutItem
Lambda order-processor        ──► DynamoDB PutItem (ORDER_CREATED)

Admin ──► GET /api/v1/admin/activity?userId=... ──► DynamoDB Query (userId + timestamp)
```

Bảng được thiết kế với `hash_key = userId`, `range_key = timestamp`, và sử dụng TTL trên trường `expireAt` (tự động xóa record sau 90 ngày, hoàn toàn không cần chạy job dọn dẹp thủ công).

#### 1. Kiểm tra cấu trúc bảng

Để xác minh thiết kế bảng:
1. Trên **DynamoDB console**, vào mục **Tables** và chọn `fashion-shop-prod-activity-log`.
2. Tại tab **Overview**, cấu hình Partition key = `userId` (String), Sort key = `timestamp` (String), và Billing mode đang chạy ở dạng `On-demand`.
3. Tại tab **Additional settings**, phần **Time to Live (TTL)** cho trường `expireAt` đã được thiết lập sang trạng thái `Enabled`.

![Overview](/images/5-Workshop/5.6-DynamoDB-Activity-Log/overview.png)

#### 2. Tạo dữ liệu test

Để sinh dữ liệu log hoạt động thực tế, các hành động sau được thực hiện trên trang web:
1. Đăng nhập vào web, xem lướt qua 2-3 sản phẩm, sau đó gõ tìm kiếm một từ khóa bất kỳ.
2. Đặt thêm một đơn hàng để Lambda xử lý và ghi thêm record `ORDER_CREATED` vào log.

#### 3. Xem record trực tiếp trong DynamoDB Console

Để kiểm tra dữ liệu đã vào bảng hay chưa:
1. Từ trang chi tiết bảng, nhấp vào **Explore table items**.
2. Các dòng log có `action` = `VIEW_PRODUCT`, `SEARCH` và `ORDER_CREATED` hiện rõ, ứng với cột `userId` là email vừa dùng ở bước trên.

![Explore](/images/5-Workshop/5.6-DynamoDB-Activity-Log/log.png)

#### 4. Xem qua trang Admin (không cần vào AWS Console)

Hệ thống cũng hỗ trợ xem log từ giao diện người dùng:
1. Trên web, đăng nhập tài khoản admin và điều hướng tới **Quản trị → Lịch sử hoạt động**.
2. Chọn đúng email đã test trong danh sách xổ xuống và chọn **Xem**.
3. Bảng kết quả truy xuất và hiển thị chính xác Thời gian / Hành động / Chi tiết, với dữ liệu mới nhất nằm trên cùng.

![Admin](/images/5-Workshop/5.6-DynamoDB-Activity-Log/admin_log.png)

{{% notice note %}}
Việc ghi log được cấu hình chạy ở dạng **best-effort và bất đồng bộ** (`@Async`). Do đó, nếu DynamoDB gặp sự cố tạm thời, các thao tác chính như xem sản phẩm/tìm kiếm của người dùng vẫn sẽ thành công bình thường; hệ thống chỉ bỏ qua phần ghi log chứ không trả lỗi (throw exception) về phía frontend.
{{% /notice %}}
