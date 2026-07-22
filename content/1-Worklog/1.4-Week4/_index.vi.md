---
title: "Worklog Tuần 4"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---



### Mục tiêu Tuần 4:

* Khám phá giải pháp điện toán đơn giản hóa Amazon Lightsail và triển khai dịch vụ ứng dụng đóng gói container qua Lightsail Container Services.
* Làm chủ cơ chế tự động mở rộng EC2 Auto Scaling Group (ASG) để đạt được độ sẵn sàng cao (High Availability), tính linh hoạt (Elasticity) và khả năng chịu lỗi trên nhiều Availability Zone.
* Tích hợp cơ chế kiểm tra sức khỏe (Health Check) giữa Application Load Balancer (ALB) và ASG để xây dựng hệ thống tự phục hồi (Self-healing).

### Công việc thực hiện trong tuần:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Nghiên cứu kiến trúc điện toán Amazon Lightsail và so sánh ưu nhược điểm giữa Lightsail (chi phí cố định, đơn giản) và Amazon EC2 (tùy biến sâu) | 11/05/2026 | 11/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Thực hành Lightsail Containers:** Đóng gói ứng dụng web thành Docker Image, đẩy lên Lightsail Container Service và cấu hình quy mô vận hành | 12/05/2026 | 12/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Nghiên cứu nguyên lý EC2 Auto Scaling: Launch Template, Auto Scaling Group (ASG), các ngưỡng Minimum/Maximum/Desired Capacity và Scaling Policy | 13/05/2026 | 13/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Phân tích cơ chế tích hợp Target Group giữa ALB và ASG, so sánh sự khác biệt giữa EC2 Status Check và ELB Health Check | 14/05/2026 | 14/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành ASG & High Availability:** Tạo Launch Template đa AZ, cấu hình ASG kết nối với ALB và giả lập tải CPU cao để kiểm tra cơ chế tự mở rộng Target Tracking | 15/05/2026 | 15/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Bài học & Góc nhìn rút ra tuần 4:

* **Amazon Lightsail Containers vs Khái niệm Orchestration cấp Doanh nghiệp:**
  * **Triển khai Container tối giản:** Lightsail Container Services mang lại giải pháp chạy Docker Container cực kỳ nhanh chóng mà không cần phức tạp hóa quy trình quản lý cụm Kubernetes (EKS) hay cấu hình Task Definition (ECS). Hệ thống tự động quản lý chứng chỉ SSL/TLS và cân bằng tải với chi phí dự đoán được hàng tháng.
  * **Ranh giới ứng dụng:** Lightsail rất phù hợp cho dự án vừa/nhỏ hoặc thử nghiệm sản phẩm (prototype). Tuy nhiên, các hệ thống lớn đòi hỏi quy chuẩn khắt khe vẫn phải dùng EC2/ECS/EKS để tận dụng hết sức mạnh của IAM Policy chi tiết, VPC Peering và cơ chế mở rộng theo chỉ số riêng.

* **Độ sẵn sàng cao (High Availability) vs Tính linh hoạt (Elasticity) trong EC2 Auto Scaling:**
  * **High Availability (Phân bổ đa AZ):** Việc phân bố các máy chủ ra nhiều Availability Zone (AZ) giúp bảo vệ hệ thống trước sự cố sập trung tâm dữ liệu vật lý. Nếu một AZ gặp sự cố hoàn toàn, Auto Scaling Group (ASG) sẽ tự động khởi tạo các máy chủ bù đắp ở các AZ còn lại để duy trì đúng số lượng máy chủ mong muốn (`Desired Capacity`).
  * **Elasticity (Chính sách mở rộng):** Áp dụng Target Tracking Scaling (ví dụ duy trì CPU trung bình ở mức 60%) giúp hạ tầng tự động phình to ra khi có lưu lượng đột biến và co lại khi vắng khách, tối ưu hóa chi phí vận hành ở mức tối đa.

* **Cơ chế Tự phục hồi (Self-Healing): So sánh ELB Health Check và EC2 Status Check:**
  * **Sức khỏe Phần cứng vs Sức khỏe Ứng dụng:** Mặc định EC2 Status Check chỉ phát hiện lỗi phần cứng/máy chủ ảo từ nhà mạng AWS. Nếu tiến trình Web (Nginx/Node.js) bị crash bên trong hệ điều hành, EC2 Status Check vẫn báo trạng thái "Passed" (Lành lặn).
  * **Kích hoạt ELB Health Check cho ASG:** Khi chuyển cấu hình Health Check của ASG sang dạng `ELB`, ASG sẽ liên tục giám sát mã phản hồi HTTP (ví dụ kiểm tra `200 OK` tại endpoint `/health`). Nếu ứng dụng web ngừng phản hồi, ASG sẽ lập tức đánh dấu máy chủ đó là `Unhealthy`, tiến hành tiêu hủy và khởi tạo một máy chủ mới từ Launch Template để thay thế.

* **Tối ưu hóa với Launch Template thay cho Launch Configuration:**
  * Việc sử dụng Launch Template (thay thế cho Launch Configuration đã cũ) cho phép quản lý phiên bản cấu hình (versioning), kết hợp linh hoạt giữa On-Demand Instance và Spot Instance để tiết kiệm chi phí, đồng thời nhúng các đoạn script User Data để tự động cài đặt ứng dụng ngay khi máy chủ vừa bật.