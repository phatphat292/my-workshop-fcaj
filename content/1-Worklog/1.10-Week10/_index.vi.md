---
title: "Worklog Tuần 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu Tuần 10:
* Hoàn thiện và chuẩn hóa bản thiết kế kiến trúc AWS tổng thể cho toàn bộ hệ thống dự án.
* Phối hợp chặt chẽ với các thành viên trong nhóm để rà soát toàn bộ luồng xử lý dữ liệu (end-to-end flows), đánh giá hiệu năng hệ thống và phát hiện các điểm nghẽn làm ảnh hưởng đến trải nghiệm người dùng.
* Đề xuất các giải pháp cải thiện giao diện và trải nghiệm người dùng (UI/UX) nhằm đảm bảo sự đồng bộ giữa phản hồi của giao diện với khả năng xử lý bất đồng bộ của hạ tầng đám mây.

### Các nhiệm vụ thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Tổng hợp và hợp nhất toàn bộ các sơ đồ thành phần (Edge, VPC, EC2, Database, Caching, Security và Observability) thành một bản vẽ kiến trúc AWS tổng thể hoàn chỉnh | 22/06/2026 | 22/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tiến hành rà soát khả năng chịu lỗi (Fault-Tolerance Review) trên bản vẽ tổng thể, diễn tập các kịch bản sự cố (mất kết nối AZ, Cache miss hàng loạt, RDS failover) | 23/06/2026 | 23/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - **Đánh giá luồng xử lý cùng nhóm:** Phối hợp với nhóm thực hiện trích xuất và phân tích chi tiết hành trình dữ liệu từ giao diện Frontend qua CloudFront, ALB, EC2 đến ElastiCache và RDS | 24/06/2026 | 24/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Phân tích các điểm nghẽn hiệu năng khi xử lý tác vụ và xác định các vị trí trên giao diện gây cảm giác gián đoạn cho người dùng do thời gian phản hồi (latency) của backend | 25/06/2026 | 25/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Đề xuất cải thiện UI/UX:** Xây dựng và tài liệu hóa các đề xuất tối ưu UI/UX (loading skeleton, cập nhật trạng thái bất đồng bộ, thông báo tiến độ thời gian thực) và hoàn thiện hồ sơ kiến trúc | 26/06/2026 | 26/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Bài học & góc nhìn rút ra:

* **Hoàn thiện bản vẽ tổng thể: Từ các dịch vụ đơn lẻ đến một hệ sinh thái đám mây đồng bộ:**
  * **Thách thức khi hợp nhất kiến trúc:** Thiết kế hạ tầng cloud theo từng dịch vụ riêng lẻ—như cấu hình VPC Subnet, thiết lập RDS Read Replica hay viết rule WAF—thường che khuất các xung đột hoặc điểm nghẽn khi kết nối toàn hệ thống. Việc hoàn thiện bản vẽ tổng thể đòi hỏi phải kiểm tra tính mạch lạc của toàn bộ luồng dữ liệu: từ tầng biên Edge (CloudFront/WAF) đi qua Public Subnet (ALB), chuyển tiếp vào Private Application Subnet (Auto Scaling EC2) và kết nối an toàn tới Data Subnet (RDS Multi-AZ & ElastiCache) mà không phá vỡ bất kỳ ranh giới bảo mật nào.
  * **Tối ưu sự nhất quán về cấu trúc:** Bài học quan trọng khi hoàn thiện sơ đồ tổng thể là tối giản hóa sơ đồ tuyến đường (routing topology). Việc loại bỏ các kết nối thừa giữa các AZ và thiết lập chuỗi Security Group chặt chẽ (ví dụ: Tầng Database *chỉ* chấp nhận truy cập đến từ Security Group của tầng Application) đảm bảo hệ thống vừa duy trì tính sẵn sàng cao, vừa dễ dàng đánh giá bảo mật (audit) sau này.

* **Sự gắn kết giữa độ trễ Backend hạ tầng Đám mây và Thiết kế UI/UX:**
  * **Tác động của kiến trúc Cloud lên trải nghiệm người dùng:** Hiệu năng hạ tầng quyết định trực tiếp cảm nhận của người dùng. Nếu hệ thống thực hiện các tác vụ ghi Database nặng hoặc xử lý dữ liệu phức tạp theo cơ chế đồng bộ (synchronous), giao diện Frontend sẽ rơi vào trạng thái "đóng băng" (hang/freeze) trong lúc chờ API phản hồi. Qua việc rà soát luồng xử lý dữ liệu, nhóm nhận ra rằng độ trễ xử lý ở Backend nếu không đi kèm phản hồi thị giác phù hợp trên UI sẽ khiến người dùng nhầm tưởng là ứng dụng bị lỗi.
  * **Thiết kế UI/UX theo cơ chế xử lý bất đồng bộ:** Để giải quyết vấn đề độ trễ, nhóm đã đề xuất chuyển đổi các tác vụ nặng sang mô hình xử lý bất đồng bộ (asynchronous). Thay vì bắt người dùng chờ đợi một request nghẽn, giao diện cần cung cấp ngay phản hồi tức thì (sử dụng màn hình chờ dạng Skeleton, thanh tiến trình công việc, hoặc thông báo trạng thái theo thời gian thực). Điều này giúp che đi độ trễ thực tế của đám mây, nâng cao đáng kể tốc độ phản hồi cảm nhận (perceived speed) của ứng dụng.

* **Tương tác đa chức năng & Sự gắn kết trong làm việc nhóm:**
  * **Xóa bỏ khoảng cách giữa Kỹ sư Hạ tầng và Lập trình viên:** Kỹ sư cloud không thể thiết kế kiến trúc hiệu quả nếu tách rời đội ngũ phát triển ứng dụng. Quá trình phối hợp làm việc cùng nhóm để đi qua từng luồng xử lý giúp cả team hiểu rõ cách thức quản lý trạng thái (state) ở Frontend và thiết kế API ở Backend phụ thuộc chặt chẽ như thế nào vào hành vi của hạ tầng đám mây.
  * **Đồng sở hữu góc nhìn toàn cảnh về hệ thống:** Việc cùng nhau đánh giá hành trình người dùng đã tạo ra sự hiểu biết chung trong toàn nhóm. Đội ngũ lập trình viên nắm rõ các giới hạn về mạng và cơ chế Caching, trong khi việc quy hoạch hạ tầng đám mây được tinh chỉnh sát với nhu cầu thực tế và mục tiêu sản phẩm của dự án.