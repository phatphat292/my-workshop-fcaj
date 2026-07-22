---
title: "Worklog Tuần 12"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu Tuần 12:
* Thực hiện kiểm thử tải (Load Testing), đánh giá khả năng chịu lỗi và diễn tập khôi phục sau sự cố (Disaster Recovery) trên toàn bộ hệ thống AWS đã hoàn thiện.
* Xây dựng bộ tài liệu bàn giao kỹ thuật chi tiết, tài liệu hướng dẫn vận hành (Runbook) và quy trình xử lý sự cố cho hệ thống hạ tầng.
* Bàn giao dự án, tổng kết kết quả thực tập, báo cáo trước cán bộ hướng dẫn và giảng viên, chính thức hoàn thành chương trình thực tập tốt nghiệp.

### Các nhiệm vụ thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Thực hiện kiểm thử tải (Load Testing) và diễn tập kỹ thuật Chaos Engineering: Giả lập sự cố tắt máy chủ EC2 đột ngột, ngắt kết nối AZ và ngắt nút chính RDS Primary | 06/07/2026 | 06/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Kiểm tra tổng thể các chỉ số vận hành lần cuối: Rà soát cảnh báo CloudWatch, thời hạn lưu trữ Log, độ chính xác của Grafana Dashboard và nhật ký truy cập WAF | 07/07/2026 | 07/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - **Soạn thảo tài liệu kỹ thuật:** Hoàn thiện bộ tài liệu Hướng dẫn vận hành kiến trúc (Runbook), Quy trình khôi phục sự cố (Disaster Recovery Playbook) và Quy chuẩn quản lý chi phí | 08/07/2026 | 08/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - **Bàn giao dự án:** Tiến hành báo cáo, demo thực tế hệ thống và bàn giao toàn bộ tài nguyên, hồ sơ kiến trúc cho cán bộ hướng dẫn tại đơn vị thực tập | 09/07/2026 | 09/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Tổng kết thực tập:** Hoàn thiện báo cáo thực tập tốt nghiệp 12 tuần, tổng kết các kết quả đạt được, báo cáo với giảng viên nhà trường và kết thúc đợt thực tập | 10/07/2026 | 10/07/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Bài học & góc nhìn rút ra:

* **Tầm quan trọng của Chaos Engineering & Kiểm thử chịu tải trước khi bàn giao:**
  * **Khoảng cách giữa thiết kế lý thuyết và thực tế vận hành:** Một bản vẽ kiến trúc có thể hoàn hảo trên giấy, nhưng độ tin cậy thực sự chỉ được chứng minh khi hệ thống đối mặt với sự cố thực tế. Việc chủ động tạo ra sự cố—như tắt đột ngột một máy chủ EC2 đang chạy, giả lập mất một Availability Zone, hoặc tạo lưu lượng truy cập tăng vọt vào ALB—đã giúp kiểm chứng chính xác phản ứng của toàn bộ hạ tầng.
  * **Xác nhận cơ chế tự động khôi phục (Automated Recovery):** Việc chứng kiến Auto Scaling tự động khởi tạo máy chủ mới thay thế máy chủ lỗi và RDS chuyển đổi sang nút Standby (Failover) chỉ trong dưới 60 giây đã mang lại niềm tin tuyệt đối vào thiết kế Multi-AZ. Đồng thời, ElastiCache Redis đã chứng minh vai trò giảm tải tuyệt vời khi gánh toàn bộ các truy vấn đọc trong thời gian Database chính thực hiện chuyển vùng.

* **Tính liên tục trong vận hành: Tài liệu kỹ thuật là một sản phẩm cốt lõi:**
  * **Không chỉ là Code hay Sơ đồ kiến trúc:** Một hệ thống Cloud được dựng lên dù hiện đại đến đâu cũng trở nên vô giá trị nếu đội ngũ tiếp quản không thể vận hành và bảo trì nó. Quá trình chuẩn bị bàn giao giúp mình nhận ra rằng: Tài liệu hướng dẫn vận hành (Runbook), quy trình khắc phục sự cố từng bước (Step-by-step troubleshooting) và tài liệu phân quyền bảo mật cũng quan trọng không kém gì chính các tài nguyên Cloud trên AWS Console.
  * **Cấu trúc bộ hồ sơ bàn giao:** Bộ hồ sơ bàn giao được đóng gói chỉn chu gồm 4 phần chính: (1) Danh mục tài nguyên & Sơ đồ kiến trúc tổng thể, (2) Quy chuẩn bảo mật & Phân quyền IAM/KMS/WAF, (3) Quy trình ứng phó sự cố & Khôi phục dữ liệu (DR), và (4) Hướng dẫn tối ưu hóa chi phí hàng tháng. Điều này đảm bảo bất kỳ kỹ sư nào tiếp quản sau này cũng có thể dễ dàng nắm bắt và duy trì hệ thống mà không gặp rào cản.

* **Nhìn lại hành trình 12 tuần: Sự trưởng thành về tư duy Kỹ sư Điện toán đám mây:**
  * **Biến lý thuyết thành năng lực thực tế:** Nhìn lại toàn bộ chặng đường 12 tuần—từ những bước đầu làm quen với IAM, EC2, VPC, S3 ở các tuần đầu, cho đến việc làm chủ AWS Managed AD, xây dựng Observability với Grafana, quản trị quyền ABAC, tính toán chi phí và hoàn thiện bản vẽ kiến trúc tổng thể—đây là một quá trình biến đổi mạnh mẽ về cả kỹ năng lẫn tư duy.
  * **Hình thành tư duy của một Cloud Engineer:** Làm việc với Cloud không đơn thuần là việc "bật/tắt" các dịch vụ trên giao diện AWS, mà là nghệ thuật cân bằng giữa độ tin cậy, tính bảo mật, hiệu năng và chi phí vận hành. Hoàn thành dự án thực tập này đã giúp mình tích lũy được nền tảng thực chiến vững chắc, tự tin thiết kế và vận hành các hệ thống hạ tầng đám mây đáp ứng các tiêu chuẩn khắt khe của doanh nghiệp.