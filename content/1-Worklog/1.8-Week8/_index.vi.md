---
title: "Worklog Tuần 8"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu Tuần 8:
* Nắm vững dịch vụ thư mục doanh nghiệp và quản lý định danh tập trung bằng cách khởi tạo, cấu hình AWS Managed Microsoft AD.
* Xây dựng hệ thống giám sát chuyên sâu (Observability) bằng cách kết hợp thu thập dữ liệu từ Amazon CloudWatch với giao diện trực quan hóa của Grafana.
* Đánh giá, phân tích và tối ưu hóa bản thiết kế kiến trúc AWS sơ bộ đã vẽ ở Tuần 7, nhằm đáp ứng các tiêu chuẩn về tính sẵn sàng cao, hiệu năng và chi phí theo AWS Well-Architected Framework.

### Các nhiệm vụ thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Tìm hiểu kiến trúc Active Directory trên AWS, so sánh sự khác biệt giữa AWS Managed Microsoft AD, AD Connector và Simple AD để lựa chọn giải pháp quản lý định danh phù hợp | 08/06/2026 | 08/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Thực hành AWS Managed Microsoft AD:** Khởi tạo AWS Managed Microsoft AD, cấu hình DHCP Option Sets cho VPC, thực hiện Domain Join cho các máy chủ EC2 và kiểm tra cơ chế xác thực tập trung | 09/06/2026 | 09/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - **Thực hành Giám sát & Tích hợp Grafana:** Cài đặt CloudWatch Unified Agent lên EC2 để đẩy RAM/Disk metrics, tích hợp CloudWatch làm Data Source trong Grafana qua IAM Role và thiết lập Dashboard giám sát thời gian thực | 10/06/2026 | 10/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Đánh giá bản vẽ kiến trúc 3 tầng sơ bộ của Tuần 7: Rà soát điểm nghẽn hiệu năng, các điểm lỗi đơn lẻ (SPOFs) và chi phí chưa tối ưu của NAT Gateway / Database | 11/06/2026 | 11/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành Tối ưu hóa Kiến trúc:** Tinh chỉnh bản sơ đồ kiến trúc hệ thống—bổ sung RDS Read Replica, tích hợp ElastiCache Redis làm bộ nhớ đệm, tối ưu hóa chính sách Auto Scaling và phân tầng Security Group | 12/06/2026 | 12/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Bài học & góc nhìn rút ra:

* **Quản lý định danh doanh nghiệp & Bản chất kỹ thuật của AWS Managed Microsoft AD:**
  * **Tự dựng Active Directory trên EC2 vs. Dùng dịch vụ Managed:** Ban đầu, khi cần quản lý tài khoản và phân quyền, suy nghĩ tự nhiên là tạo một máy chủ Windows Server EC2 rồi cài AD DS cho tiết kiệm. Tuy nhiên, góc nhìn thực tế vận hành cho thấy việc này vô cùng rủi ro và tốn công: phải tự cấu hình nhân bản (replication) giữa các Subnet, tự quản lý lịch backup Domain Controller, tự vá lỗi OS và duy trì HA trên nhiều AZ. Với AWS Managed Microsoft AD, AWS sẽ đứng ra quản trị toàn bộ hạ tầng bên dưới của các Domain Controller trên 2 AZs độc lập. Trong khi đó, người quản trị vẫn sở hữu một Active Directory chuẩn Microsoft, toàn quyền dùng các công cụ như RSAT, cấu hình GPO, Kerberos và LDAP mà không phải lo bảo trì hạ tầng.
  * **Bài học về DHCP Option Sets khi Domain Join:** Một chi tiết kỹ thuật cực kỳ quan trọng trong thực tế là việc gia nhập Domain của EC2 không chỉ dừng lại ở việc thông mạng (ping thấy nhau). Mặc định, VPC cấp địa chỉ DNS server là `AmazonProvidedDNS` (chỉ phân giải các tên miền internet và nội bộ VPC). Để máy chủ EC2 tìm thấy các bản ghi SRV của Domain Controller, bắt buộc phải tạo một DHCP Option Set mới trong VPC, khai báo IP DNS chính là IP hai giao diện mạng (ENI) của AWS Managed AD. Nếu không thực hiện bước này, lệnh gia nhập domain sẽ liên tục báo lỗi do không định vị được miền xác thực.

* **Xây dựng hệ thống giám sát chuyên sâu (Observability): Sự kết hợp giữa CloudWatch và Grafana:**
  * **Tại sao chỉ dùng CloudWatch mặc định là chưa đủ?** Các chỉ số mặc định của CloudWatch hoạt động ở tầng Hypervisor (máy chủ vật lý bên ngoài). Hypervisor chỉ đo được % CPU tiêu thụ, dung lượng mạng In/Out và tốc độ đọc/ghi đĩa cứng. Nó hoàn toàn không thể "nhìn vào trong" hệ điều hành (Guest OS) để biết RAM đang còn bao nhiêu % hay dung lượng ổ đĩa phân vùng `/var/log` đã bị đầy chưa. Đây chính là "điểm mù" khiến nhiều ứng dụng bị crash do cạn RAM (OOM) mà CloudWatch không hề phát cảnh báo. Cài đặt CloudWatch Unified Agent bên trong OS máy chủ giúp chủ động đẩy các chỉ số RAM, Swap, Disk Space và Log ứng dụng về CloudWatch Log Groups.
  * **Phân tách nhiệm vụ giữa Thu thập (CloudWatch) và Trực quan hóa (Grafana):** Thay vì chỉ theo dõi dạng biểu đồ cơ bản trên AWS Console, việc tích hợp CloudWatch làm Data Source cho Grafana thông qua cơ chế IAM Role (Assume Role / Instance Profile) mang lại giải pháp giám sát rất mạnh mẽ. Cách làm này không cần dùng Access Key cố định (tăng rủi ro bảo mật) mà vẫn giúp xây dựng được Dashboard tập trung, hiển thị mật độ thông tin cao và dễ quan sát. Mô hình này làm rõ vai trò: CloudWatch đóng vai trò lưu trữ và thu thập dữ liệu thô (Data Collector), còn Grafana đảm nhận vai trò hiển thị và phân tích chuyên sâu (Visualization).

* **Tối ưu hóa kiến trúc hệ thống: Từ bản vẽ sơ bộ đến hạ tầng sẵn sàng cho sản xuất (Production-Ready):**
  * **Giải tỏa áp lực Database bằng cơ chế Caching và Phân tách truy vấn:** Ở bản vẽ sơ bộ Tuần 7, toàn bộ tải đọc/ghi đều dồn vào một instance RDS duy nhất. Khi lưu lượng người dùng tăng cao, RDS sẽ nhanh chóng bị nghẽn ở số lượng kết nối (connection limit) và giới hạn IOPS của đĩa cứng. Sau khi phân tích, kiến trúc Tuần 8 đã được bổ sung cụm ElastiCache (Redis) đứng trước Database để lưu Cache các dữ liệu ít biến động và Session người dùng, giúp phản hồi truy vấn ở tốc độ microsecond. Đồng thời, cấu hình thêm RDS Read Replica để gánh toàn bộ các câu lệnh đọc (SELECT), giải phóng tài nguyên cho RDS Primary tập trung xử lý các thao tác ghi (INSERT/UPDATE/DELETE).
  * **Bài học cân bằng giữa Tính sẵn sàng cao (Availability) và Chi phí (Cost):** Thiết kế kiến trúc luôn là bài toán đánh đổi. Đặt NAT Gateway ở tất cả các Subnet trên từng AZ sẽ đảm bảo tính cô lập lỗi tối đa, nhưng lại phát sinh chi phí duy trì hàng tháng rất lớn. Bằng cách rà soát lại luồng dữ liệu thực tế của ứng dụng, mình đã điều chỉnh lại cách phân tầng Subnet và Security Group: vừa đảm bảo các thành phần quan trọng (như DB, App Server) vẫn nằm ở Private Subnet và có khả năng failover Multi-AZ, vừa tối ưu hóa được chi phí truyền dữ liệu giữa các AZ (Cross-AZ Data Transfer) trong giai đoạn hiện tại của dự án.