---
title: "Worklog Tuần 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---



### Mục tiêu Tuần 7:

* Làm chủ Mạng phân phối nội dung toàn cầu (CDN) và cơ chế điện toán biên với Amazon CloudFront để giảm độ trễ và bảo vệ các máy chủ gốc (Origin).
* Triển khai Origin Access Control (OAC) và tích hợp chứng chỉ SSL/TLS từ AWS Certificate Manager (ACM) để đảm bảo bảo mật toàn trình.
* Phân tích yêu cầu bài toán thực tế và bắt đầu phác thảo bản thiết kế kiến trúc đám mây AWS sơ bộ cho dự án tốt nghiệp.

### Công việc thực hiện trong tuần:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Nghiên cứu kiến trúc CloudFront CDN: Trạm biên (Edge Location), Bộ nhớ đệm vùng (Regional Edge Cache), Cache Behavior, TTL và các loại Origin (S3, ALB, HTTP gốc) | 01/06/2026 | 01/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Nghiên cứu cơ chế Điện toán biên (CloudFront Functions vs Lambda@Edge) và phân phối nội dung bảo mật bằng Signed URL/Signed Cookie kết hợp chứng chỉ ACM | 02/06/2026 | 02/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - **Thực hành CloudFront & OAC:** Khởi tạo CloudFront Distribution, cấu hình Origin Access Control (OAC) để bảo vệ S3 Origin và kiểm thử cơ chế lưu đệm trạm biên cũng như xoá đệm (Cache Invalidation) | 03/06/2026 | 03/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Phân tích yêu cầu dự án: Xác định các nghiệp vụ cốt lõi, yêu cầu độ sẵn sàng cao, băng thông dự kiến và các quy chuẩn bảo mật bắt buộc | 04/06/2026 | 04/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành Phác thảo Kiến trúc:** Bắt đầu vẽ sơ đồ kiến trúc AWS đa tầng (3-tier) đa AZ sơ bộ kết hợp VPC, ALB, EC2 Auto Scaling, RDS MySQL, S3 và CloudFront | 05/06/2026 | 05/06/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Bài học & Góc nhìn rút ra tuần 7:

* **Tăng tốc nội dung và Bảo vệ máy chủ gốc nhờ Amazon CloudFront:**
  * **Giảm thời gian phản hồi đầu tiên (TTFB):** CloudFront lưu bản sao các tài nguyên tĩnh (hình ảnh, CSS/JS) và cả nội dung động tại hàng trăm trạm biên (Edge Location) trên khắp thế giới. Người dùng sẽ tải dữ liệu từ trạm biên gần họ nhất thông qua hạ tầng mạng backbone tối ưu của AWS, thay vì phải gửi yêu cầu đường dài về tận máy chủ gốc nằm ở các AWS Region xa xôi.
  * **Giảm tải cho máy chủ gốc (Origin Offloading):** Lưu đệm ở trạm biên giúp hấp thụ tới 80-90% lượt truy cập GET. Điều này giúp giảm áp lực lớn cho các máy chủ EC2 phía sau và S3 Origin khỏi nguy cơ quá tải khi truy cập tăng đột biến, đồng thời giảm đáng kể chi phí băng thông đường truyền.

* **Cơ chế Bảo mật: Origin Access Control (OAC) vs Legacy OAI:**
  * **Triệt tiêu nguy cơ phơi bày S3 Bucket:** Việc mở S3 Bucket công khai ra internet luôn chứa đựng rủi ro rò rỉ dữ liệu nghiêm ngặt.
  * **Lợi thế của OAC:** Origin Access Control (OAC) cho phép cấu hình S3 Bucket Policy chỉ chấp nhận các yêu cầu đã được ký duyệt bởi đúng CloudFront Distribution chỉ định (`AWS:SourceArn`). OAC hỗ trợ đầy đủ các tiêu chuẩn bảo mật hiện đại như mã hóa KMS server-side, các phương thức HTTP `PUT/POST`, và điều kiện IAM chi tiết, thay thế hoàn toàn công nghệ OAI đã cũ.

* **Các mô hình Điện toán biên: CloudFront Functions vs Lambda@Edge:**
  * **CloudFront Functions:** Các đoạn mã JavaScript siêu nhẹ chạy trong thời gian dưới 1 millisecond ngay tại hơn 600 trạm biên. Rất thích hợp cho các tác vụ đơn giản tần suất cao như chuyển hướng URL, thêm/sửa HTTP Header và kiểm tra token JWT cơ bản.
  * **Lambda@Edge:** Môi trường Node.js hoặc Python đầy đủ chạy tại các Regional Edge Cache. Thích hợp cho các xử lý nghiệp vụ phức tạp, gọi API tới hệ thống bên ngoài hoặc biến đổi cấu trúc dữ liệu request/response body trước khi gửi về máy chủ gốc.

* **Chuyển đổi Yêu cầu Bài toán thành Bản thiết kế Kiến trúc Cloud Sơ bộ:**
  * **Cân bằng các trụ cột AWS Well-Architected:** Việc thiết kế kiến trúc ban đầu là bài toán cân bằng giữa Độ tin cậy (triển khai đa AZ trên các subnet), Bảo mật (Private Subnet cho DB, CDN công khai ở biên) và Hiệu năng.
  * **Kiến trúc 3 Tầng chuẩn mực (3-Tier Architecture):** Phân chia rõ ràng các tầng — Tầng Biên/Mạng (CloudFront + ALB), Tầng Ứng dụng (Auto Scaling EC2/Containers), và Tầng Dữ liệu (RDS Multi-AZ) — giúp tạo ra bộ khung sạch sẽ, đảm bảo sự cô lập và khả năng mở rộng độc lập ngay từ ngày đầu tiên.