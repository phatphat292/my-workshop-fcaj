---
title: "Worklog Tuần 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---



### Mục tiêu Tuần 3:

* Loại bỏ hoàn toàn việc hardcode credential bằng cách gán IAM Role cho máy chủ EC2 thông qua dịch vụ Instance Metadata (IMDSv2).
* Làm chủ quy trình phát triển ứng dụng đám mây với môi trường Cloud9 IDE tích hợp sẵn quyền truy cập AWS.
* Cấu hình Amazon S3 để lưu trữ đối tượng an toàn, triển khai website tĩnh và quản lý quyền truy cập chi tiết qua Bucket Policy.
* Khởi tạo và quản trị cơ sở dữ liệu quan hệ Amazon RDS trong Private Subnet sử dụng DB Subnet Group và kỹ thuật chuỗi Security Group (SG Chaining).

### Công việc thực hiện trong tuần:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Nghiên cứu cơ chế IAM Role cho EC2 và Instance Profile <br> - Khám phá môi trường lập trình đám mây AWS Cloud9 và cách tùy chỉnh cấu hình | 04/05/2026 | 04/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Thực hành IAM & Cloud9:** Khởi tạo Cloud9, tạo IAM Role với quyền S3 tối thiểu, gán cho EC2 và kiểm tra việc tự sinh credential tạm thời qua AWS CLI | 05/05/2026 | 05/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu kiến trúc Amazon S3: Bucket, Object, tính năng S3 Block Public Access, Bucket Policy và ACL <br> - Cấu hình S3 Static Website Hosting với trang index và error tùy chỉnh | 06/05/2026 | 06/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Nghiên cứu dịch vụ cơ sở dữ liệu quan hệ Amazon RDS: Chọn Database Engine (MySQL/PostgreSQL), kiến trúc Multi-AZ và DB Subnet Group | 07/05/2026 | 07/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành RDS & Tích hợp ứng dụng:** Khởi tạo RDS MySQL trong Private Subnet cô lập, cấu hình Security Group và kết nối thành công từ ứng dụng Web trên EC2 | 08/05/2026 | 08/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Bài học & Góc nhìn rút ra tuần 3:

* **Cơ chế hoạt động của IMDSv2 và Credential tạm thời từ IAM Role:**
  * **Cách EC2 lấy quyền mà không cần hardcode key:** Khi gán IAM Role cho EC2, AWS sẽ tạo ra một *Instance Profile*. Ứng dụng chạy trên EC2 sẽ truy vấn vào địa chỉ IP nội bộ Instance Metadata Service (`http://169.254.169.254/latest/meta-data/iam/security-credentials/`) để lấy các chuỗi Key ngắn hạn do AWS STS (Security Token Service) tự động phát hành.
  * **Tầm quan trọng của IMDSv2 đối với bảo mật:** IMDSv2 bắt buộc phải khởi tạo một Session Token bằng lệnh `PUT` trước khi thực hiện các lệnh `GET` lấy thông tin. Cơ chế theo phiên này triệt tiêu hoàn toàn rủi ro từ lỗ hổng SSRF (Server-Side Request Forgery), ngăn chặn kẻ tấn công đánh cắp credential của máy chủ nếu ứng dụng web bị hack.

* **Ranh giới bảo mật Amazon S3 và Cơ chế Website tĩnh:**
  * **Lớp van an toàn Block Public Access:** Mặc định AWS bật 4 tùy chọn "Block Public Access" ở cấp tài khoản và cấp Bucket để chống rò rỉ dữ liệu. Để host website tĩnh công khai, mình hiểu ra rằng chỉ tắt Block Public Access là chưa đủ — cần phải viết thêm một file JSON Bucket Policy để cấp quyền truy cập đọc (`s3:GetObject`) rõ ràng cho người dùng internet.
  * **Phân biệt Object Storage vs Block Storage:** Khác với EBS (lưu trữ khối cắm trực tiếp vào máy chủ hoạt động ở cấp độ file system của OS), S3 là kho lưu trữ đối tượng dạng phẳng được truy xuất hoàn toàn qua API HTTPS. S3 cho khả năng mở rộng dung lượng không giới hạn và độ bền dữ liệu lên tới 99.999999999% (11 số 9) nhờ tự động sao lưu dữ liệu ra nhiều Availability Zone (AZ) khác nhau.

* **Cô lập Cơ sở dữ liệu và Kỹ thuật Chuỗi Security Group (Security Group Chaining) trong Amazon RDS:**
  * **Cô lập bằng DB Subnet Group:** Một cơ sở dữ liệu RDS bắt buộc phải gán với một DB Subnet Group chứa các Subnet nằm ở ít nhất 2 Availability Zone. Trong thực tế, các Subnet này bắt buộc phải là *Private Subnet* (không có đường truyền ra Internet Gateway), đảm bảo Database hoàn toàn không thể bị tấn công trực tiếp từ bên ngoài internet.
  * **Kỹ thuật Security Group Chaining (Tối ưu phân quyền mạng):** Thay vì mở cổng 3306 cho một dải IP cố định (ví dụ `10.0.1.0/24`), giải pháp chuẩn mực là *điền thẳng ID của Security Group phía Web App* (ví dụ `sg-web-app`) vào mục Inbound Rule của Security Group phía RDS. Cách làm này giúp Database chỉ chấp nhận kết nối từ các EC2 mang đúng nhãn Security Group đó, tự động thích ứng khi hệ thống mở rộng (Auto Scaling) mà không cần sửa IP thủ công.

* **Tối ưu hiệu suất phát triển Cloud với AWS Cloud9:**
  * Việc dùng Cloud9 mang lại một môi trường Ubuntu/Amazon Linux được cấu hình sẵn đầy đủ các công cụ như AWS CLI, Docker và Git.
  * Sự tích hợp mượt mà giữa Cloud9 và hệ thống phân quyền tự động giúp việc chạy thử nghiệm các kiến trúc đa tầng (Multi-tier) trở nên nhanh chóng, không làm nặng hay ô nhiễm môi trường máy tính cá nhân.