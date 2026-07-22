---
title: "Worklog Tuần 9"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu Tuần 9:
* Thiết kế và triển khai cơ chế kiểm soát truy cập chi tiết bằng AWS IAM kết hợp chiến lược ABAC (Attribute-Based Access Control) dựa trên Resource Tags.
* Nghiên cứu, tích hợp và cấu hình bổ sung các dịch vụ bảo mật và kết nối hạ tầng cốt lõi như AWS KMS, Secrets Manager, AWS WAF và VPC Endpoints vào bản thiết kế kiến trúc.
* Sơ đồ hóa và hoàn thiện các luồng kết nối dịch vụ, ranh giới bảo mật và đường truyền dữ liệu nội bộ cho hệ thống Multi-AZ.

### Các nhiệm vụ thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Tìm hiểu mô hình phân quyền dựa trên thuộc tính (ABAC) so với phân quyền theo vai trò (RBAC), phân tích các Condition Keys dựa trên Tag trong IAM (`aws:ResourceTag`, `aws:RequestTag`) | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Thực hành IAM & Quản trị Tag:** Xây dựng các chính sách IAM Policy phân quyền chi tiết dựa trên Tag (ví dụ: `Environment=Production`, `Owner=PhatPH`), kiểm tra ranh giới quyền hạn (Permission Boundaries) và áp dụng chính sách bắt buộc gắn Tag | 16/06/2026 | 16/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Nghiên cứu giải pháp kết nối mạng riêng tư và mã hóa: VPC Endpoints (Gateway vs. Interface Endpoints), AWS KMS Customer Managed Keys (CMK), AWS Secrets Manager và AWS WAF Web ACLs | 17/06/2026 | 17/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - **Thực hành Tích hợp Dịch vụ AWS:** Khởi tạo S3 Gateway Endpoint, Secrets Manager Interface Endpoint, tạo khóa KMS CMK để mã hóa dữ liệu RDS/S3 và gắn WAF Web ACLs bảo vệ CloudFront/ALB | 18/06/2026 | 18/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành Tích hợp Kiến trúc:** Cập nhật và mở rộng bản thiết kế kiến trúc hệ thống—thể hiện rõ ranh giới bảo mật WAF, đường truyền riêng qua VPC Endpoints, luồng giải mã KMS và phân tầng IAM | 19/06/2026 | 19/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Bài học & góc nhìn rút ra:

* **Quản trị quyền hạn mở rộng: Chuyển dịch từ RBAC sang ABAC nhờ Resource Tags:**
  * **Hạn chế của mô hình RBAC truyền thống:** Trong một hạ tầng đám mây phát triển nhanh, việc dùng Role-Based Access Control (RBAC) đòi hỏi người quản trị phải liên tục tạo thêm Role và Policy mới cho từng nhóm người dùng hoặc môi trường. Cách làm này dẫn đến hiện tượng "bội bội chính sách" (policy sprawl), rất tốn công bảo trì và dễ bỏ sót lỗ hổng bảo mật.
  * **Sức mạnh của ABAC trong doanh nghiệp:** Mô hình Attribute-Based Access Control (ABAC) giải quyết triệt để vấn đề này bằng cách phân quyền dựa trên thuộc tính (Tag) của cả người dùng (Principal) và tài nguyên (Resource). Chỉ cần một câu lệnh điều kiện trong IAM Policy so sánh `${aws:PrincipalTag/Department}` với `${aws:ResourceTag/Department}`, quyền hạn sẽ tự động áp dụng đúng cho các tài nguyên mới tạo mà không cần sửa đổi IAM Policy.
  * **Kỹ thuật ép buộc gắn Tag (Tag Enforcement):** Một kinh nghiệm thực tế quan trọng là sử dụng Condition Key `aws:RequestTag`. Bằng cách chặn các hành động như `ec2:RunInstances` hay `s3:CreateBucket` nếu người dùng không truyền đúng các Tag quy định (như `Environment`, `Owner`), hệ thống sẽ ngăn chặn hoàn toàn việc xuất hiện các tài nguyên "vô chủ" gây lãng phí chi phí hoặc vi phạm quy chuẩn an toàn.

* **Bảo mật đường truyền nội bộ với VPC Endpoints & Mã hóa dữ liệu KMS:**
  * **Triệt tiêu lưu lượng ra Internet nhờ VPC Endpoints:** Mặc định, khi các máy chủ EC2 ở Private Subnet giao tiếp với các dịch vụ AWS như S3, Secrets Manager hay KMS, dữ liệu phải đi qua NAT Gateway ra các public endpoint của AWS. Việc khởi tạo Gateway Endpoints (dành cho S3/DynamoDB) và Interface Endpoints / AWS PrivateLink (dành cho Secrets Manager/KMS) giúp ép toàn bộ luồng dữ liệu chạy hoàn toàn trong đường truyền nội bộ của AWS. Điều này không chỉ giúp tăng tốc độ phản hồi, nâng cao tiêu chuẩn bảo mật mà còn cắt giảm đáng kể chi phí xử lý dữ liệu của NAT Gateway.
  * **Tách biệt thông tin nhạy cảm khỏi Code & Quản lý khóa tập trung:** Việc lưu cứng (hardcode) mật khẩu Database hay API Key trong mã nguồn là một rủi ro cực kỳ nghiêm trọng. Tích hợp AWS Secrets Manager cho phép ứng dụng lấy thông tin xác thực một cách linh hoạt tại thời điểm chạy (runtime) thông qua IAM Role. Kết hợp với khóa mã hóa riêng (KMS Customer Managed Keys), toàn bộ dữ liệu lưu trữ trên RDS, S3, EBS đều được mã hóa phong bì (envelope encryption) và ghi lại toàn bộ nhật ký truy cập trên CloudTrail.

* **Hoàn thiện bản vẽ kiến trúc: Xây dựng giải pháp phòng thủ nhiều lớp (Defense-in-Depth):**
  * **Phòng thủ nhiều lớp trên toàn bộ hệ thống:** Việc đưa các dịch vụ bổ trợ vào bản vẽ kiến trúc làm rõ hơn nguyên tắc "Defense-in-Depth". AWS WAF đứng ở tầng biên (CloudFront/ALB) để chặn các đòn tấn công ứng dụng web phổ biến (SQL Injection, XSS, HTTP Flood); Security Groups / Network ACLs kiểm soát chặt chẽ luồng mạng ở tầng hạ tầng; còn IAM và KMS đảm bảo an toàn ở tầng định danh và dữ liệu.
  * **Tính rõ ràng trong sơ đồ kiến trúc:** Bài học lớn nhất khi bổ sung liên kết dịch vụ là không được để lại các "điểm giả định ngầm". Mọi luồng truy cập—từ việc EC2 lấy secret từ Secrets Manager đến việc CloudFront lấy log đẩy về S3—đều phải được vẽ rõ ràng trên sơ đồ. Điều này giúp phát hiện sớm các điểm nghẽn về cấu hình mạng hoặc phân quyền IAM trước khi tiến hành triển khai thực tế.