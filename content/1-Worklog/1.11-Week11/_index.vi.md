---
title: "Worklog Tuần 11"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu Tuần 11:
* Thành thạo công cụ AWS Pricing Calculator để lập dự toán chi phí vận hành (TCO) chi tiết và đề xuất các giải pháp tối ưu hóa chi phí hạ tầng cho dự án.
* Tiến hành đánh giá toàn diện kiến trúc hệ thống, rà soát rủi ro an toàn thông tin dựa trên trụ cột Bảo mật (Security Pillar) của AWS Well-Architected Framework.
* Đề xuất và triển khai các giải pháp tăng cường bảo mật (Security Hardening), sẵn sàng cho công tác kiểm tra và bàn giao dự án.

### Các nhiệm vụ thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Tìm hiểu mô hình tính phí của AWS (On-Demand, Compute Savings Plans, Reserved Instances, Spot Instances) và các phân tầng lưu trữ (S3 Storage Classes) | 29/06/2026 | 29/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Thực hành AWS Pricing Calculator:** Lập bảng dự toán chi phí hàng tháng cho toàn bộ kiến trúc tổng thể (EC2 Auto Scaling, RDS Multi-AZ, ElastiCache, CloudFront, ALB, NAT Gateway) | 30/06/2026 | 30/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Xây dựng chiến lược tối ưu chi phí: Cấu hình S3 Lifecycle Rules (chuyển log sang Glacier Instant Retrieval), tinh chỉnh lịch Auto Scaling và đề xuất Rightsizing tài nguyên | 01/07/2026 | 01/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - **Thực hành Rà soát Rủi ro & Bảo mật:** Đánh giá an toàn kiến trúc bằng các tiêu chí AWS Security Hub, kiểm tra IAM Access Analyzer và rà soát luồng truy cập Security Group | 02/07/2026 | 02/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Triển khai Tăng cường Bảo mật (Hardening):** Bật VPC Flow Logs để giám sát lưu lượng bất thường, thắt chặt luật Security Group, áp dụng TLS 1.3 cho CloudFront/ALB và hoàn thiện báo cáo giảm thiểu rủi ro | 03/07/2026 | 03/07/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Bài học & góc nhìn rút ra:

* **Quản trị tài chính đám mây & Bài học lập dự toán với AWS Pricing Calculator:**
  * **Phát hiện các "chi phí ẩn" trong hạ tầng Cloud:** Khi bắt tay vào tính toán chi phí thực tế, bài học lớn nhất là hóa đơn hàng tháng không chỉ dừng lại ở giá thuê máy chủ EC2 hay Database RDS. Các khoản phí chi trả cho truyền tải dữ liệu ra Internet (Data Transfer Out), phí duy trì hàng giờ của NAT Gateway cộng với phí xử lý dung lượng (per-GB processing fee), cùng với IOPS khởi tạo của EBS mới là những yếu tố gây bùng nổ chi phí ngoài dự kiến nếu không được mô hình hóa kỹ từ đầu.
  * **Chiến lược tối ưu hóa chi phí không ảnh hưởng đến độ tin cậy:** Việc xây dựng dự toán giúp nhận ra sức mạnh của việc kết hợp các mô hình cam kết sử dụng. Đối với các tài nguyên nền tảng chạy 24/7 (như RDS Primary hoặc số lượng EC2 tối thiểu trong Auto Scaling), việc áp dụng Compute Savings Plans cam kết 1 năm giúp giảm từ 40–60% chi phí so với On-Demand. Ngoài ra, thiết lập S3 Lifecycle Rules để tự động chuyển log truy cập và bản backup RDS từ S3 Standard sang S3 Glacier Instant Retrieval sau 30 ngày giúp ngăn chặn đà tăng chi phí lưu trữ theo thời gian.

* **Rà soát rủi ro kiến trúc & Trụ cột Bảo mật (Security Pillar):**
  * **Bảo mật là một quá trình liên tục (Continuous Process):** Quá trình rà soát hệ thống cho thấy bảo mật đám mây không phải là một cấu hình làm một lần rồi bỏ đó. Việc kiểm tra toàn bộ hạ tầng đã phát hiện ra những "lỗ hổng nhỏ" dễ bị bỏ qua—như các rule Security Group mở quá rộng ở chiều ra (Egress), Bucket lưu log S3 chưa bật mã hóa mặc định, hoặc Load Balancer còn chấp nhận các giao thức TLS cũ.
  * **Giám sát chuyên sâu với VPC Flow Logs:** Việc bật VPC Flow Logs và phân tích luồng mạng giúp phát hiện chính xác các kết nối không hợp lệ giữa các Subnet. Việc chuyển đổi từ cách mở CIDR Block rộng sang việc chỉ cho phép các Security Group cụ thể giao tiếp với nhau (Security Group Chaining) giúp cô lập hoàn toàn ranh giới mạng, đưa hệ thống tiến gần hơn đến mô hình Zero-Trust.

* **Bài toán cân bằng: Chi phí vs. Tính sẵn sàng & Bảo mật:**
  * **Tránh bẫy "Lãng phí do dư thừa" hoặc "Rủi ro do quá tiết kiệm":** Thiết kế kiến trúc cloud luôn là nghệ thuật dung hòa giữa ngân sách và sự an toàn. Việc dư thừa tài nguyên (over-provisioning) sẽ đảm bảo hiệu năng nhưng gây lãng phí ngân sách lớn, ngược lại cắt giảm chi phí quá đà sẽ dẫn đến nguy cơ sập hệ thống (SPOF) hoặc bị tấn công do thiếu các lớp phòng thủ.
  * **Tìm điểm tối ưu cho kiến trúc:** Dự án đã chứng minh rằng các giải pháp kiến trúc thông minh—chẳng hạn như dùng VPC Endpoint thay cho NAT Gateway để truy cập S3/KMS (vừa cắt giảm phí NAT vừa tăng bảo mật), hay dùng Auto Scaling Warm Pools thay vì bật sẵn máy chủ EC2 lớn 24/7—sẽ giúp hệ thống vừa đạt tiêu chuẩn an toàn cao, vừa giữ cho chi phí vận hành ở mức tối ưu nhất.