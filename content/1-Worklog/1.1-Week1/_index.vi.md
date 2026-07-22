---
title: "Worklog Tuần 1"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---



### Mục tiêu Tuần 1:

* Thiết lập môi trường thực hành AWS (sandbox) chuẩn chỉ với credit tài trợ để thoải mái vọc vạch mà không lo phát sinh chi phí ngoài ý muốn.
* Hiểu rõ cách AWS cô lập tài nguyên đám mây ở cấp độ mạng bằng VPC, Subnet, Internet Gateway và Route Table.

### Công việc thực hiện trong tuần:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2   | - Bắt đầu chương trình First Cloud AI Journey <br> - Tạo tài khoản AWS và nhận $100 credit đầu tiên để dựng môi trường thực hành an toàn | 20/04/2026 | 20/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Hoàn thành 5 nhiệm vụ khám phá trên AWS Skill Builder <br> - Nhận thêm $100 credit và làm quen với thao tác trên giao diện AWS Console | 21/04/2026 | 21/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Nghiên cứu kiến trúc VPC (Virtual Private Cloud) <br> - Tìm hiểu cách dải IP (CIDR block) định hình không gian mạng riêng trên Cloud | 22/04/2026 | 22/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Phân biệt Public Subnet và Private Subnet <br> - Tìm hiểu cơ chế Internet Gateway (IGW) đóng vai trò làm cổng ra/vào cho lưu lượng Internet | 23/04/2026 | 23/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Học cách Route Table điều hướng lưu lượng giữa các Subnet và mạng bên ngoài <br> - **Thực hành:** Tự dựng VPC riêng gồm Public/Private Subnet, gắn IGW và cấu hình Route Table | 24/04/2026 | 24/04/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Bài học & Góc nhìn rút ra tuần 1:

* **Tầm quan trọng của môi trường Sandbox:** Việc nhận ngay credit tài trợ từ đầu giúp mình có môi trường vọc vạch thoải mái, tự tin khởi tạo dịch vụ mà không bị áp lực về chi phí phát sinh bất ngờ.
* **Bản chất của việc cô lập trên Cloud:** Mình hiểu ra VPC chính là một trung tâm dữ liệu ảo (virtual data center) riêng của mình trên AWS. Mọi tài nguyên bên trong mặc định được cô lập hoàn toàn, trừ khi mình chủ động mở đường cho lưu lượng ra ngoài.
* **Điều gì thực sự tạo nên một "Public Subnet":** Trước đây mình nghĩ Public Subnet có một cờ cấu hình riêng, nhưng thực tế một Subnet chỉ trở thành "Public" khi Route Table của nó có một tuyến đường (`0.0.0.0/0`) trỏ thẳng ra Internet Gateway (IGW). Nếu không có dòng đó, mọi kết nối sẽ chỉ nằm gói gọn trong VPC.
* **Tư duy Bảo mật:** Việc chia tách hạ tầng ngay từ đầu — đưa máy chủ Web ra Public Subnet và giấu Database vào Private Subnet — là bước căn bản nhưng cực kỳ quan trọng để áp dụng mô hình bảo mật nhiều lớp (defense-in-depth) trên AWS.