---
title : "Chi phí & Cleanup"
date : 2026-07-12
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

#### Ước tính chi phí hàng tháng (chạy 24/7)

| Dịch vụ | Chi phí ước tính |
|---|---|
| NAT Gateway x2 | ~$85-90 |
| ALB | ~$20 |
| EC2 (Auto Scaling Group, 2-4x t3.micro) | ~$20-40 |
| RDS (db.t3.micro, Multi-AZ) | ~$35 |
| ElastiCache Redis (cache.t3.micro) | ~$13-15 |
| WAF (Web ACL + 4 rule) | ~$9 |
| S3 + CloudFront + DynamoDB + SQS + Lambda + SES + SNS + CloudWatch | ~$2-3 |
| **Tổng ước tính** | **~$184-212/tháng** |

{{% notice tip %}}
**Chi phí EC2 giờ là một khoảng chứ không phải số cố định** — Auto Scaling Group giữ baseline 2 instance (~$184-192/tháng tổng) và tự scale lên tới 4 khi CPU tải cao kéo dài (target-tracking 60% trung bình, tối đa ~$204-212/tháng tổng). Phần chênh lệch chỉ tính theo số giờ ASG thực sự scale-out, không cộng cố định vào hóa đơn hàng tháng.
{{% /notice %}}

{{% notice tip %}}
**NAT Gateway chiếm gần một nửa hóa đơn** — khoản chi phí lớn nhất và dễ bị bỏ qua nhất. Có thể giảm khoảng $43/tháng bằng cách dùng 1 NAT Gateway thay vì 2 (đánh đổi: mất khả năng chịu lỗi cấp AZ cho traffic ra internet của ứng dụng — một rủi ro chấp nhận được đối với dự án nhỏ).
{{% /notice %}}



#### Cleanup — dỡ toàn bộ hệ thống

Bởi vì toàn bộ hệ thống được quản lý bằng Infrastructure-as-Code (IaC), khi dọn dẹp kết thúc workshop chỉ cần gỡ bỏ các thành phần theo trình tự:

{{% notice warning %}}
Việc dỡ hệ thống sẽ xóa dữ liệu **vĩnh viễn**. Mặc dù RDS tạo một snapshot tự động cuối cùng trước khi bị xóa, S3 và DynamoDB lại không được backup tự động. Thao tác này chỉ thực hiện khi chắc chắn không còn cần dùng hệ thống nữa.
{{% /notice %}}

1. **Phần hạ tầng xử lý** dỡ trước (do có ít thành phần phụ thuộc hơn) — bao gồm VPC, EC2, ALB, RDS, và toàn bộ các dịch vụ hỗ trợ.
2. Sau đó đến **phần hạ tầng giao diện** — bao gồm S3 site bucket, CloudFront và WAF.
3. Cuối cùng, rà soát bằng tay để kiểm tra không còn resource nào sót lại — đây là các resource dễ bị quên vì không nằm trong phần định nghĩa hạ tầng ban đầu:
   + **ECR** → repository `fashion-shop-backend` (chứa image ứng dụng) — gỡ riêng.
   + **CloudWatch Logs** → các log group vẫn còn giữ log cũ tính phí lưu trữ nên cần xóa sạch.
   + **S3** → đối với site bucket có versioning, cần xóa hết mọi version của các object trước khi bucket có thể xóa hoàn toàn.


