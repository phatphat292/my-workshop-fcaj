---
title: "Worklog Tuần 5"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---



### Mục tiêu Tuần 5:

* Triển khai hệ thống giám sát tập trung, gom log tự động và thiết lập cảnh báo sự cố tài nguyên bằng Amazon CloudWatch.
* Làm chủ cơ chế phân giải tên miền (DNS), các loại bản ghi và chiến lược điều hướng lưu lượng bằng Amazon Route 53.
* Cấu hình Private Hosted Zone để phân giải tên miền nội bộ VPC và xây dựng hạ tầng DNS hybrid linh hoạt.

### Công việc thực hiện trong tuần:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Nghiên cứu kiến trúc CloudWatch: Metrics, Alarms, Logs và CloudWatch Unified Agent <br> - Phân biệt thông số lớp máy chủ vật lý (Hypervisor) và chỉ số tùy chỉnh lớp hệ điều hành (OS) | 18/05/2026 | 18/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Thực hành CloudWatch:** Cài đặt CloudWatch Agent lên EC2 để thu thập dung lượng RAM/Ổ đĩa, đẩy log hệ thống về Log Group và tạo cảnh báo email qua SNS khi CPU tăng cao | 19/05/2026 | 19/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu nguyên lý Route 53 DNS: Các loại bản ghi (A, AAAA, CNAME, MX, TXT, Alias) và phân biệt Public vs Private Hosted Zone | 20/05/2026 | 20/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Phân tích các chính sách điều hướng Route 53 (Routing Policies): Simple, Weighted, Latency, Failover (kết hợp Health Check) và Geolocation | 21/05/2026 | 21/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành Route 53:** Tạo Public Hosted Zone trỏ tên miền về ALB bằng bản ghi Alias, và thiết lập Private Hosted Zone cho phân giải nội bộ VPC | 22/05/2026 | 22/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Bài học & Góc nhìn rút ra tuần 5:

* **Sự khác biệt giữa Hypervisor Metrics và Custom Metrics trong CloudWatch:**
  * **Giới hạn quan sát của Hypervisor:** Mặc định AWS CloudWatch chỉ giám sát máy chủ EC2 từ lớp ảo hóa (Hypervisor) bên ngoài. Lớp này chỉ nhìn thấy % CPU, lưu lượng Mạng (Network I/O) và tốc độ Đọc/Ghi đĩa, hoàn toàn không thấy được dữ liệu bên trong hệ điều hành của khách hàng.
  * **Tầm quan trọng của CloudWatch Agent:** Việc theo dõi % dung lượng RAM sử dụng hay khoảng trống ổ đĩa thực tế nằm hoàn toàn ở lớp kernel của Linux/Windows. Muốn thu thập các chỉ số này, bắt buộc phải cài đặt và cấu hình CloudWatch Unified Agent chạy ngầm bên trong máy chủ để đẩy Metric tùy chỉnh về CloudWatch API.

* **Gom Log tập trung và Tự động hóa cảnh báo qua SNS:**
  * **Tách biệt Log khỏi ổ cứng tạm thời:** Việc đẩy trực tiếp log ứng dụng (`/var/log/nginx/access.log`, log hệ thống) về CloudWatch Log Groups giúp bảo vệ dữ liệu log không bị mất khi máy chủ EC2 bị phá hủy do Auto Scaling hoặc sự cố phần cứng.
  * **Tự động hóa cảnh báo:** Tích hợp CloudWatch Alarms với dịch vụ gửi thông báo Amazon SNS giúp chuyển đổi tư duy vận hành từ thụ động kiểm tra log sang chủ động nhận cảnh báo. Ngay khi chỉ số vượt ngưỡng (ví dụ CPU > 85% trong 5 phút), SNS sẽ tự động bắn email cảnh báo hoặc kích hoạt webhook xử lý tức thì.

* **Sức mạnh của bản ghi Alias Record trong Route 53 so với CNAME chuẩn:**
  * **Giới hạn tên miền gốc (Zone Apex):** Tiêu chuẩn DNS quốc tế (RFC 1034) cấm sử dụng bản ghi CNAME cho tên miền gốc (`example.com`) nếu tại đó đã có các bản ghi khác tồn tại.
  * **Ưu thế của Route 53 Alias Record:** Alias Record là tính năng độc quyền của AWS cho phép trỏ thẳng tên miền gốc (`example.com`) tới các tài nguyên AWS (như ALB, CloudFront, S3 Bucket) mà không vi phạm chuẩn DNS. Đặc biệt, các truy vấn Route 53 sử dụng Alias trỏ tới tài nguyên AWS hoàn toàn miễn phí.

* **Private Hosted Zone cho Bảo mật Doanh nghiệp và Mạng Hybrid:**
  * Việc sử dụng Route 53 Private Hosted Zone cho phép tự định nghĩa các tên miền nội bộ (ví dụ `db.internal.company.local`) chỉ có giá trị phân giải trong phạm vi các VPC được chỉ định.
  * Giải pháp này loại bỏ việc phải phơi bày các địa chỉ IP nội bộ ra mạng DNS công cộng, giữ cho cấu trúc các microservice ẩn hoàn toàn trước các nguy cơ do thám từ internet.