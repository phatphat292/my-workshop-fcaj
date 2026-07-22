---
title: "AWS Direct Connect + AWS Transit Gateway"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

Hôm nay mình muốn chia sẻ một pattern kết nối cực kỳ mạnh mẽ và được khuyến nghị sử dụng khi xây dựng hạ tầng hybrid giữa on-premise và AWS Cloud: **AWS Direct Connect + AWS Transit Gateway**.

Theo tài liệu chính thức của AWS, khi kết hợp **AWS Direct Connect** với **AWS Transit Gateway** thông qua **Transit Virtual Interface (Transit VIF)** attachment đến **Direct Connect Gateway**, bạn có thể kết nối private dedicated line từ mạng nội bộ của mình đến nhiều regional centralized routers một cách dễ dàng và hiệu quả.

### Tại sao nên dùng giải pháp này?

* **Transit Gateway đóng vai trò trung tâm (hub)**: Mỗi Transit Gateway hoạt động như một network transit hub, giúp interconnect nhiều VPC trong cùng Region. Bạn chỉ cần quản lý routing configuration tại một nơi duy nhất thay vì cấu hình riêng lẻ cho từng VPC.
* **Kết nối private & dedicated**: Tránh đi qua Internet công cộng, mang lại độ tin cậy cao, latency thấp và băng thông ổn định.
* **Lợi ích kinh tế & hiệu suất**:
    * Giảm đáng kể chi phí network so với các giải pháp VPN thông thường.
    * Tăng throughput (băng thông) tối đa.
    * Trải nghiệm mạng nhất quán, phù hợp cho các ứng dụng yêu cầu hiệu suất cao như migration dữ liệu lớn, disaster recovery, hoặc real-time applications.
* **Quản lý dễ dàng hơn**:
    * Chỉ cần một kết nối Direct Connect duy nhất có thể phục vụ cho nhiều VPC hoặc VPN trong cùng Region.
    * Hỗ trợ advertise prefixes linh hoạt giữa on-premise và AWS.
    * Dễ dàng scale khi hệ thống phát triển.

---

### Kiến trúc cơ bản

Bạn sẽ có:
1. Một **Transit Gateway**.
2. Một **Direct Connect Gateway**.
3. **Association** giữa chúng.
4. **Transit VIF** gắn vào Direct Connect Gateway.

Kết quả là traffic từ on-premise có thể đi qua dedicated connection rồi được Transit Gateway phân phối thông minh đến các VPC cần thiết. Rất sạch sẽ và dễ vận hành!

Giải pháp này đặc biệt phù hợp với các doanh nghiệp đang chuyển đổi số mạnh mẽ, có nhiều workload trên AWS, hoặc cần kết nối ổn định giữa data center truyền thống và cloud. Mình thấy nhiều công ty đã áp dụng mô hình này để tối ưu chi phí và nâng cao security cho hạ tầng hybrid.

![AWS Direct Connect + Transit Gateway Details](/images/3-BlogsPosted/image3.jpeg)

* **Nguồn tham khảo chính thức:** [AWS Whitepaper: AWS Direct Connect + AWS Transit Gateway](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/aws-direct-connect-aws-transit-gateway.html)