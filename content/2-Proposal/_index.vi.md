---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
## Giải pháp Điện toán Đám mây AWS Toàn diện cho Nền tảng Bán lẻ Trực tuyến Tự động và Bảo mật

### 1. Tóm tắt điều hành
Dự án nhằm xây dựng một hệ thống Web E-commerce hiệu năng cao, bảo mật và có khả năng tự động mở rộng cho chuỗi cửa hàng shop thời trang. Hệ thống ban đầu được thiết kế để xử lý ổn định lưu lượng truy cập từ khách hàng mua sắm trực tuyến, có khả năng tự động co giãn (Auto Scaling Group) từ 2 lên đến 4 máy chủ EC2 dựa trên ngưỡng CPU trung bình (target tracking 60%) khi traffic tăng đột biến. Hệ thống tận dụng tối đa hạ tầng AWS Cloud để cung cấp trải nghiệm mua sắm thời gian thực không độ trễ, phân tích giỏ hàng tự động, đồng thời bảo mật tuyệt đối dữ liệu người dùng và thanh toán nhờ tích hợp Amazon Cognito và AWS WAF.

### 2. Tuyên bố vấn đề
* **Vấn đề hiện tại:** Hệ thống quản lý cửa hàng thời trang truyền thống hoặc các web hosting đơn lẻ thường bị sập nguồn/nghẽn mạng khi có lượng truy cập đột biến vào các mùa giảm giá. Không có khả năng lưu trữ và phân tích hành vi giỏ hàng theo thời gian thực, quản lý kho hàng thủ công, dễ bị tấn công mạng (DDoS, SQL Injection) và chi phí vận hành máy chủ vật lý cố định quá tốn kém ngay cả trong những tháng thấp điểm.
* **Giải pháp:** Nền tảng triển khai một kiến trúc đa tầng (Multi-Tier Architecture) phân tách rõ ràng trên AWS:
    * **Tầng Giao diện & Bảo mật mạng:** Khách hàng truy cập thông qua **AWS CloudFront (CDN)** giúp tối ưu tốc độ tải trang bằng cách fetch dữ liệu tĩnh từ **Amazon S3**, được bảo vệ nghiêm ngặt bởi **AWS WAF** để kiểm tra và chặn các truy cập độc hại.
    * **Tầng Xử lý & Cân bằng tải:** Yêu cầu (HTTP Requests) đi qua **Internet Gateway** đến bộ cân bằng tải **Application Load Balancer (ALB)**, phân phối đều traffic đến các máy chủ **Amazon EC2** đặt trong các Private Subnet thuộc các Availability Zone (AZ) khác nhau để đảm bảo tính sẵn sàng cao.
    * **Tầng Dữ liệu & Caching:** Toàn bộ thông tin sản phẩm, đơn hàng và khách hàng lưu trữ tại cụm **AWS RDS** (Cơ sở dữ liệu quan hệ) có cơ chế Multi-AZ replication. Trạng thái phiên làm việc (Session) và giỏ hàng (Cart) được tối ưu tốc độ đọc/ghi nhờ **AWS ElastiCache (Redis)**.
    * **Tầng Xử lý Bất đồng bộ & Phân tích Đơn hàng (Serverless):** Đơn hàng mới sẽ được đẩy vào hàng đợi **AWS SQS**, kích hoạt **AWS Lambda** để xử lý đơn hàng tự động mà không làm nghẽn hệ thống chính. Dữ liệu lịch sử đơn hàng và log hệ thống được ghi nhận vào **Amazon DynamoDB** và **Amazon S3** thông qua **S3 Gateway**.
* **Lợi ích và hoàn vốn đầu tư (ROI):**
    * **Tối ưu hóa chi phí:** Nhờ tính năng Auto Scaling Group (ASG), hệ thống chỉ trả tiền cho lượng tài nguyên compute thực tế sử dụng. Chi phí vận hành tự động giảm tối đa vào giờ thấp điểm.
    * **Tăng tỷ lệ chuyển đổi:** Website tải nhanh <1s nhờ CloudFront và ElastiCache giúp giữ chân khách hàng mua sắm, giảm tỷ lệ bỏ rơi giỏ hàng.
    * **An toàn tuyệt đối:** Tự động phát hiện và chặn đứng 99% các cuộc tấn công mạng phổ biến thông qua WAF.
    * **Thời gian hoàn vốn:** Ước tính từ 3–6 tháng nhờ tối ưu chi phí phần cứng vật lý và gia tăng doanh số bán hàng trực tuyến toàn quốc.

---

### 3. Kiến trúc giải pháp
Nền tảng áp dụng kiến trúc chuẩn Highly Available & Fault Tolerant trên AWS, chia làm các phân vùng Public và Private Subnet nghiêm ngặt nhằm cô lập tài nguyên hệ thống core.

**Mô hình kiến trúc hệ thống**

![E-commerce System Architecture](/images/2-Proposal/mohinh.png)

#### Dịch vụ AWS sử dụng
* **AWS WAF & CloudFront:** Lọc mã độc, chống DDoS và phân phối nội dung tĩnh/động toàn cầu.
* **Amazon VPC (Public/Private Subnets & NAT Gateway):** Cách ly mạng nội bộ, NAT Gateway cho phép EC2 trong Private Subnet tải cập nhật bảo mật mà không bị lộ IP ra Internet.
* **Application Load Balancer (ALB) & EC2:** Tự động cân bằng tải và xử lý logic ứng dụng web bán hàng.
* **AWS ElastiCache:** Tăng tốc truy vấn giỏ hàng và dữ liệu đệm sản phẩm hot.
* **Amazon RDS:** Hệ quản trị cơ sở dữ liệu quan hệ chính (quản lý người dùng, tồn kho, hóa đơn).
* **AWS SQS & AWS Lambda:** Tiếp nhận và xử lý sự kiện thanh toán/đơn hàng theo cơ chế serverless bất đồng bộ.
* **Amazon DynamoDB & S3:** Lưu trữ cơ sở dữ liệu NoSQL (log sự kiện, lịch sử duyệt đồ) và tài nguyên hình ảnh sản phẩm (Media asset).
* **AWS CloudWatch & AWS SNS:** Giám sát hiệu năng hệ thống (CPU, RAM, Network) và tự động gửi cảnh báo qua SMS/Email khi có sự cố.

---

### 4. Triển khai kỹ thuật
#### Các giai đoạn triển khai
1. **Giai đoạn 1: Thiết kế Topology và Database Schema (Tháng 1):** Thiết kế sơ đồ mạng VPC chi tiết, cấu hình bảo mật Security Groups, IAM Roles và chuẩn hóa cơ sở dữ liệu quan hệ cho Shop thời trang.
2. **Giai đoạn 2: Hiện thực hóa Hạ tầng (IaC) (Tháng 2):** Viết script Terraform/AWS CDK để tự động khởi tạo ALB, EC2 Auto Scaling, RDS và các chính sách định tuyến CloudFront.
3. **Giai đoạn 3: Phát triển Backend & Tích hợp Serverless (Tháng 2 - Tháng 3):** Kết nối mã nguồn web với ElastiCache, viết hàm Lambda xử lý luồng đơn hàng từ SQS và ghi log vào DynamoDB.
4. **Giai đoạn 4: Kiểm thử chịu tải (Load Testing) & Triển khai (Tháng 3):** Giả lập 10,000 người dùng cùng lúc bằng các công cụ test script để kiểm tra khả năng co giãn của hệ thống, sau đó cấu hình WAF và Go-Live.

#### Yêu cầu kỹ thuật
* **Hạ tầng Web:** Source code (Spring Boot/Java) đóng gói Docker Container chạy trên EC2 hoặc tích hợp trực tiếp vào Launch Template của Auto Scaling Group.
* **Kết nối Dữ liệu:** Đảm bảo độ trễ kết nối từ EC2 đến RDS và ElastiCache thấp nhất (<5ms) nhờ đặt cùng một Availability Zone cục bộ hoặc thiết lập VPC Endpoints (S3 Gateway).

---

### 5. Lộ trình & Mốc triển khai
* **Tháng 1:** Hoàn thiện bản vẽ kiến trúc chi tiết, thiết lập tài khoản AWS Organization và phân quyền IAM.
* **Tháng 2:** Triển khai môi trường Staging (VPC, EC2, RDS, Caching, SQS). Khởi tạo giỏ hàng thử nghiệm.
* **Tháng 3:** Hoàn thiện tích hợp luồng Serverless (Lambda, DynamoDB), cấu hình hệ thống giám sát CloudWatch Alarms và SNS. Test tải toàn diện và chuyển giao hệ thống sang môi trường Production.

---

### 6. Ước tính ngân sách
* **NAT Gateway (x2, 1 mỗi AZ):** Khoản chi phí lớn nhất, phục vụ traffic ra internet của EC2 trong private subnet (~$85 - $90/tháng).
* **Amazon EC2 (t3.micro, Auto Scaling Group 2-4) & ALB:** Baseline 2 node duy trì tính sẵn sàng cao, tự scale lên tới 4 khi CPU tải cao kéo dài (~$40 - $60/tháng tùy mức scale).
* **Amazon RDS (db.t3.micro - Multi-AZ):** Lưu trữ dữ liệu hệ thống cốt lõi an toàn (~$35/tháng).
* **AWS ElastiCache (Redis, cache.t3.micro):** Cache dữ liệu catalog (~$13 - $15/tháng).
* **AWS WAF:** Web ACL và 4 rule quản lý bảo vệ lớp ngoài (~$9/tháng).
* **Amazon S3, CloudFront, DynamoDB, SQS, Lambda, SES, SNS & CloudWatch:** Chi phí theo lượng tiêu thụ thực tế, hầu hết nằm trong Free Tier (~$2 - $3/tháng).

> **Tổng chi phí ước tính:** ~$184 - $212/tháng (baseline ~$184-192 khi chạy 2 EC2; tăng dần tới $212 chỉ trong những giờ Auto Scaling Group thực sự scale-out lên mức tối đa 4 — không cộng cố định vào hóa đơn. Có thể tối ưu sâu hơn bằng Reserved Instances hoặc Savings Plans cho EC2/RDS, hoặc giảm còn 1 NAT Gateway).

---

### 7. Đánh giá rủi ro
#### Ma trận rủi ro
* **Tấn công từ chối dịch vụ (DDoS/Brute Force):** Ảnh hưởng cao, xác suất trung bình.
* **Xảy ra lỗi đồng bộ giỏ hàng/Hết hàng ảo (Race Condition):** Ảnh hưởng cao, xác suất thấp.
* **Vượt ngân sách AWS do cấu hình sai vòng lặp Auto Scaling:** Ảnh hưởng trung bình, xác suất trung bình.

#### Chiến lược giảm thiểu & Kế hoạch dự phòng
* **Chống DDoS:** Chặn từ lớp ngoài cùng bằng các managed rule group của AWS WAF (Common Rule Set, SQL Injection, Known Bad Inputs) kết hợp một rule giới hạn tốc độ (rate-based) chặn IP gửi quá 2.000 request/5 phút.
* **Đồng bộ dữ liệu:** Đơn hàng được ghi đồng bộ vào RDS ngay tại thời điểm checkout — trước khi đưa vào hàng đợi — nên transaction database, chứ không phải thứ tự hàng đợi, đảm bảo không trùng đơn. Một SQS queue tiêu chuẩn (standard, không FIFO) sau đó tách việc gửi email/ghi log khỏi luồng phản hồi khách hàng, kèm dead-letter queue (tối đa 3 lần thử) để cô lập message lỗi thay vì âm thầm mất dữ liệu.
* **Kiểm soát chi phí:** Cấu hình **AWS Budgets** tự động gửi thông báo qua **AWS SNS** đến email quản trị viên khi chi phí thực tế chạm ngưỡng 80% ngân sách dự kiến.

---

### 8. Kết quả kỳ vọng
* Website hoạt động ổn định với tỷ lệ Uptime đạt 99.99%.
* Khả năng chịu tải tăng gấp 5 lần so với hệ thống cũ nhưng chi phí tối ưu hơn 40% nhờ tính năng tự động tắt bớt tài nguyên khi vắng khách.
* Hệ thống dữ liệu hành vi mua sắm được lưu trữ tập trung tại S3/DynamoDB, sẵn sàng làm nguồn tài nguyên quý giá cho phòng marketing phân tích xu hướng thời trang và tích hợp các mô hình AI gợi ý sản phẩm (Recommendation System) trong tương lai.