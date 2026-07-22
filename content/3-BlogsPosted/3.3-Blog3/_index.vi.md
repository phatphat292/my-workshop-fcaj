---
title: "AWS DX – DXGW với AWS Transit Gateway Multi-Regions và AWS Public Peering"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---


Hôm nay mình muốn chia sẻ sâu hơn về một mô hình kết nối Hybrid Connectivity cực kỳ mạnh mẽ và thực tế từ tài liệu AWS Whitepaper: **AWS DX – DXGW kết hợp AWS Transit Gateway, hỗ trợ Multi-Regions và AWS Public Peering**.

Dù đây là whitepaper tham khảo lịch sử (một số chi tiết có thể đã được AWS tối ưu thêm theo thời gian), nhưng giá trị thiết kế và tư duy kiến trúc mà nó mang lại vẫn cực kỳ hữu ích cho những ai đang xây dựng hệ thống hybrid scale lớn giữa On-Premises và AWS Cloud.

### Tổng quan về mô hình

Mô hình được xây dựng với các thành phần chính sau:
* Nhiều AWS Region hoạt động đồng thời.
* **Dual AWS Direct Connect connections** đến các Direct Connect locations độc lập (tăng tính sẵn sàng cao, tránh rủi ro single point of failure).
* Một data center on-premises chính kết nối dual về AWS.
* Sử dụng **AWS Direct Connect Gateway (DXGW)** làm trung tâm kết nối.
* Kết hợp **AWS Transit Gateway** để quản lý traffic thông minh.
* Hỗ trợ high scale với hàng nghìn VPC mỗi Region.

**Hình dung đơn giản:** Từ data center của bạn, hai đường cáp Direct Connect riêng biệt sẽ đi vào AWS → qua DXGW → Transit Gateway phân phối traffic đến toàn bộ hệ sinh thái VPC đa Region. Đồng thời, **Public Virtual Interface (Public VIF)** cho phép truy cập trực tiếp các dịch vụ public của AWS (như Amazon S3, DynamoDB, CloudFront…) với tốc độ cao, độ trễ thấp và bảo mật tối ưu.

---

### Những ưu điểm nổi bật khiến mình thích mô hình này

* **Kết nối Private & Public thông minh**: Public VIF giúp bypass internet hoàn toàn khi truy cập public resources, giảm chi phí egress và tăng security.
* **Khả năng mở rộng tương lai**: Dễ dàng kết nối thêm VPC hoặc DX connection ở các Region khác mà không cần thiết kế lại từ đầu.
* **Mesh Connectivity mạnh mẽ**: Transit Gateway cho phép xây dựng full-mesh hoặc partial-mesh giữa hàng loạt VPC một cách đơn giản, thay vì phải quản lý hàng trăm peering connection thủ công.
* **Inter-Region & Inter-VPC traffic**: Giao tiếp giữa các VPC cross Region trở nên mượt mà nhờ Transit Gateway peering.
* **Tích hợp Security & SD-WAN linh hoạt**: Bạn có thể dễ dàng đưa third-party virtual appliances (Firewall, WAF, SD-WAN…) vào Transit Gateway để centralized security. Đây là điểm rất hay cho enterprise.

---

### Khả năng Scale & Hiệu suất

* Hỗ trợ hàng nghìn VPC trên một Transit Gateway chỉ qua single BGP session – cực kỳ mạnh về scale.
* Single Transit VIF cho mỗi DX connection, nhưng bạn có thể thêm nhiều DX connection khi traffic tăng.
* **Lưu ý quan trọng** về route limits (inbound/outbound) theo quota của AWS Direct Connect. Nên tham khảo kỹ trước khi triển khai.

---

### Những lưu ý thực tế khi áp dụng

* **Chi phí**: Ngoài phí Direct Connect, bạn sẽ chịu thêm chi phí Transit Gateway data processing cho traffic on-prem ↔ AWS.
* **Security Group limitation**: Security Group của VPC remote không reference được qua Transit Gateway. Nếu cần reference SG cross-VPC thì phải kết hợp VPC Peering.
* **Alternative design**: Với quy mô nhỏ hơn, VPC Peering có thể thay thế, nhưng khi VPC nhiều thì quản lý sẽ trở nên phức tạp và khó maintain.
* **High Availability**: Luôn ưu tiên dual connections + multi-location để đạt resilience cao nhất.

Tổng thể, đây là mô hình lý tưởng cho các doanh nghiệp đang trong giai đoạn hybrid transformation, muốn kết nối private ổn định, băng thông cao, chi phí kiểm soát tốt và sẵn sàng scale lên multi-account, multi-region trong tương lai.

![AWS DX – DXGW with AWS Transit Gateway Multi-Regions and AWS Public Peering](/images/3-BlogsPosted/image4.png)

* **Nguồn tham khảo chính thức:** [AWS Whitepaper – Hybrid Connectivity](https://docs.aws.amazon.com/whitepapers/latest/hybrid-connectivity/aws-dx-dxgw-with-aws-transit-gateway-multi-regions-and-aws-public-peering.html)