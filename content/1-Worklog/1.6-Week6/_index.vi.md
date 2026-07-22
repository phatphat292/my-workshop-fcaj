---
title: "Worklog Tuần 6"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---



### Mục tiêu Tuần 6:

* Làm chủ việc khởi tạo, truy vấn và tự động hóa quản lý tài nguyên qua giao diện dòng lệnh AWS CLI sử dụng Named Profile và cú pháp trích xuất dữ liệu JMESPath.
* Nắm vững nguyên lý thiết kế cơ sở dữ liệu NoSQL, chiến lược chọn Partition Key và cơ chế đánh chỉ mục trong Amazon DynamoDB.
* Triển khai bộ nhớ đệm trên RAM tốc độ cao với Amazon ElastiCache (Redis) để giảm tải truy xuất cho cơ sở dữ liệu chính và đạt độ trễ phản hồi dưới 1 millisecond.

### Công việc thực hiện trong tuần:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Tìm hiểu nguyên lý hoạt động của AWS CLI, quản lý tài khoản qua Named Profile (`~/.aws/credentials`), các định dạng đầu ra (`json`, `table`, `text`) và cú pháp lọc dữ liệu JMESPath (`--query`) | 25/05/2026 | 25/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Thực hành AWS CLI:** Viết script Bash sử dụng AWS CLI để tự động kiểm tra trạng thái máy chủ EC2, kiểm tra tài nguyên VPC và trích xuất cấu trúc dữ liệu JSON | 26/05/2026 | 26/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Nghiên cứu cơ sở dữ liệu NoSQL Amazon DynamoDB: Khái niệm Partition Key (PK), Sort Key (SK), Global/Local Secondary Index (GSI/LSI) và hai chế độ On-Demand vs Provisioned Capacity | 27/05/2026 | 27/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Nghiên cứu hệ thống bộ nhớ đệm Amazon ElastiCache (so sánh Redis vs Memcached) và các chiến lược caching: Lazy Loading (Cache-Aside) vs Write-Through | 28/05/2026 | 28/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành DynamoDB & ElastiCache:** Tạo bảng DynamoDB, triển khai cụm ElastiCache Redis trong Private Subnet và cấu hình bộ nhớ đệm cho ứng dụng để tăng tốc truy xuất dữ liệu | 29/05/2026 | 29/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Bài học & Góc nhìn rút ra tuần 6:

* **Tự động hóa Quản trị Hạ tầng bằng AWS CLI & Trích xuất dữ liệu nâng cao với JMESPath:**
  * **Bước chuyển đổi từ Console sang Automation:** Việc chuyển từ thao tác click chuột trên giao diện Web Console sang sử dụng các lệnh CLI là bước đệm quan trọng để tiến tới tự động hóa DevOps và Hạ tầng dạng mã nguồn (Infrastructure as Code - IaC).
  * **Bộ lọc dữ liệu chính xác với JMESPath (`--query`):** Kết quả trả về từ AWS CLI thường là các chuỗi JSON rất lớn. Việc làm chủ cú pháp JMESPath lồng trực tiếp trong lệnh CLI (ví dụ `--query "Reservations[*].Instances[*].{ID:InstanceId,State:State.Name}"`) giúp lọc đúng thuộc tính cần lấy ngay trong Terminal script mà không cần cài thêm các công cụ bên ngoài như `jq`.

* **Tư duy Thiết kế Dữ liệu NoSQL trong Amazon DynamoDB:**
  * **Tránh hiện tượng "Hot Partition":** Khác với cơ sở dữ liệu quan hệ (RDBMS) tập trung vào chuẩn hóa dữ liệu và JOIN bảng, DynamoDB phân tán dữ liệu vật lý dựa trên thuật toán Hash của Partition Key (PK). Một thiết kế PK chuẩn phải có tính phân tán cao (ví dụ `user_id` hoặc `device_uuid`) để phân bổ đều tải Read/Write Capacity (RCU/WCU), tránh tình trạng một node lưu trữ bị quá tải ("hot partition").
  * **Chỉ mục phụ Secondary Index (GSI vs LSI):** Local Secondary Index (LSI) dùng chung Partition Key với bảng chính nhưng khác Sort Key và bắt buộc phải tạo ngay khi khởi tạo bảng. Global Secondary Index (GSI) có thể tự do chọn Partition Key và Sort Key hoàn toàn mới, có thể tạo bất cứ lúc nào và có hạn ngạch băng thông (Throughput) riêng biệt.

* **Kiến trúc Caching với ElastiCache (Redis) & Mô hình Lazy Loading:**
  * **Giảm tải cho Database chính:** Các cơ sở dữ liệu chính (RDS hay DynamoDB) được thiết kế cho việc lưu trữ bền vững trên ổ đĩa, nên I/O đĩa sẽ thành điểm nghẽn khi có hàng triệu lượt đọc. Đặt một cụm ElastiCache chạy hoàn toàn trên RAM phía trước Database sẽ giúp phản hồi các truy vấn phổ biến với độ trễ cực thấp (sub-millisecond).
  * **Chiến lược Lazy Loading (Cache-Aside):** Ứng dụng sẽ kiểm tra ElastiCache trước. Nếu có dữ liệu (*Cache Hit*), trả về ngay. Nếu không có (*Cache Miss*), ứng dụng mới đọc từ Database chính, sau đó ghi dữ liệu đó vào ElastiCache kèm thời gian sống (TTL) rồi mới phản hồi cho người dùng. Việc đặt TTL hợp lý giúp bộ nhớ đệm không bị phình to dữ liệu rác và luôn tự làm mới.

* **Lựa chọn Engine: Amazon ElastiCache Redis vs Memcached:**
  * **Memcached:** Lựa chọn tối ưu cho các bài toán lưu trữ dạng Key-Value đơn giản, đa luồng (multi-threaded), dữ liệu chỉ là các chuỗi text hoặc HTML đơn giản và không yêu cầu tính năng sao lưu hay nhân bản phức tạp.
  * **Redis:** Bắt buộc phải chọn khi bài toán yêu cầu các cấu trúc dữ liệu nâng cao (Hashes, Lists, Sets, Geospatial), cần lưu trữ bền vững (Snapshots/AOF), hỗ trợ nhân bản đa vùng (Multi-AZ replication) và tự động chuyển vùng khi có sự cố (Automatic Failover).