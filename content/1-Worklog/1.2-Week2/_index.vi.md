---
title: "Worklog Tuần 2"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---



### Mục tiêu Tuần 2:

* Làm chủ dịch vụ IAM (Quản lý danh tính & quyền truy cập) để áp dụng nguyên tắc cấp quyền tối thiểu (Least Privilege).
* Đi sâu vào thiết lập mạng riêng ảo (VPC) nâng cao và nắm vững các lớp bảo vệ lưu lượng mạng.
* Khởi tạo, cấu hình an toàn và quản trị máy chủ ảo EC2 nằm bên trong VPC riêng.

### Công việc thực hiện trong tuần:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Tìm hiểu kiến thức cốt lõi về IAM (Identity and Access Management) <br> - Phân biệt IAM User, Group, Role, Policy và tầm quan trọng của xác thực 2 lớp (MFA) | 27/04/2026 | 27/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Thực hành IAM:** Khởi tạo user cho các thành viên, viết Policy bằng file JSON và bật MFA để khóa hoàn toàn tài khoản Root | 28/04/2026 | 28/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Nghiên cứu các lớp bảo mật trong VPC: Phân biệt Security Group (Stateful) và Network ACL (Stateless) <br> - Quy hoạch lại sơ đồ mạng VPC đa Subnet | 29/04/2026 | 29/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu các dòng máy chủ EC2 (Instance Types), lựa chọn AMI, Key Pair và loại ổ đĩa lưu trữ EBS | 30/04/2026 | 30/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành EC2:** Khởi tạo EC2 bên trong VPC riêng, mở cổng SSH/HTTP trên Security Group và kết nối máy chủ qua Key Pair | 01/05/2026 | 01/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Bài học & Góc nhìn rút ra tuần 2:

* **Tư duy Bảo mật & Nguyên tắc Quyền Tối thiểu (Least Privilege) trong IAM:**
  * **Triết lý khóa tài khoản Root:** Mình hiểu rõ lý do vì sao tài khoản Root phải được kích hoạt MFA và cất giữ cẩn thận ngay sau khi tạo. Mọi công việc vận hành hàng ngày phải được phân chia cho các IAM User hoặc IAM Role với phạm vi quyền hạn được giới hạn nghiêm ngặt.
  * **Rủi ro của Credential tĩnh vs Lợi ích của IAM Role:** Lưu trữ Access Key/Secret Key dưới dạng chuỗi ký tự cố định trên máy cá nhân hoặc nhúng vào code luôn tiềm ẩn nguy cơ rò rỉ rất cao. Việc sử dụng IAM Role — tự động phát hành credential tạm thời có cơ chế tự xoay vòng (rotation) — là chuẩn mực bắt buộc khi cấp quyền cho máy chủ EC2 hoặc ứng dụng giao tiếp với các dịch vụ AWS khác.

* **Cơ chế lọc gói tin: Phân biệt Security Group và Network ACL:**
  * **Lọc gói tin Stateful ở cấp độ ENI (Security Group):** Security Group gắn trực tiếp vào card mạng ảo (ENI) của máy chủ. Do có tính chất *Stateful*, khi một gói tin đi vào được cho phép (ví dụ cổng 80), hệ thống quản lý ảo hóa (Hypervisor) sẽ tự ghi nhớ trạng thái kết nối đó và cho phép luồng dữ liệu phản hồi đi ra mà không cần quan tâm đến các quy tắc Outbound.
  * **Lọc gói tin Stateless ở ranh giới Subnet (NACL):** Network ACL đóng vai trò như bức tường lửa bảo vệ toàn bộ Subnet và hoạt động theo cơ chế *Stateless*. Mọi gói tin đi vào hay đi ra đều bị kiểm tra độc lập theo thứ tự ưu tiên của rule. Để một kết nối thành công qua NACL, mình phải mở cả chiều Inbound (ví dụ cổng 443) lẫn chiều Outbound (cho phép dải cổng tạm thời - Ephemeral Ports `1024-65535` gửi phản hồi về cho client).

* **Quy trình Chẩn đoán và Tự sửa lỗi kết nối EC2 (Troubleshooting):**
  * Khi thực hiện lệnh SSH (`ssh -i key.pem ec2-user@<IP>`) mà bị treo hoặc báo lỗi `Connection Timed Out`, điều đó khẳng định tắc nghẽn nằm ở hạ tầng mạng hoặc các lớp bảo mật chứ không phải do hệ điều hành máy chủ bị lỗi.
  * Mình rút ra quy trình kiểm tra 5 bước chuẩn hóa:
    1. **IP & Subnet:** Máy chủ đã nằm ở Public Subnet và được cấp Public/Elastic IP chưa?
    2. **Route Table:** Tuyến đường `0.0.0.0/0` đã được trỏ thẳng ra Internet Gateway (IGW) chưa?
    3. **Security Group:** Đã mở cổng Inbound 22 cho IP cá nhân chưa?
    4. **Network ACL:** Subnet NACL có đang chặn cổng 22 ở chiều vào hoặc cổng Ephemeral ở chiều ra không?
    5. **Quyền File Key:** Phân quyền riêng tư cho file `.pem` (`chmod 400 key.pem`) để bộ client OpenSSH chấp nhận kết nối.

* **Bản chất Kiến trúc Tách biệt giữa Lưu trữ (EBS) và Điện toán (EC2):**
  * Việc hiểu Amazon EBS thực chất là một ổ cứng mạng (Network Attached Storage - SAN) kết nối qua đường truyền nội bộ chứ không phải đĩa cứng vật lý cắm trực tiếp vào máy chủ là một bước ngoặt về tư duy kiến trúc.
  * Thiết kế tách biệt này giúp bảo vệ dữ liệu tối đa: khi máy chủ EC2 bị sự cố phần cứng hoặc bị xoá (terminate), ổ đĩa EBS vẫn tồn tại độc lập, cho phép dễ dàng tháo rời và gắn sang một máy chủ mới mà không bị mất mát dữ liệu.