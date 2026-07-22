---
title: "Event 1"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---



# Báo cáo tóm tắt: “FCAJ Community Day 2026”

### Mục tiêu sự kiện

- Tạo không gian kết nối, chia sẻ kiến thức và truyền cảm hứng cho các thành viên trong cộng đồng công nghệ.
- Chia sẻ kiến thức thực tế, xu hướng mới và kinh nghiệm triển khai dự án thực tế từ các chuyên gia trong lĩnh vực Điện toán đám mây (Cloud Computing), Trí tuệ nhân tạo (AI), DevOps và Platform Engineering.
- Định hình tư duy và các kỹ năng thiết yếu cho nhân sự IT (đặc biệt là sinh viên và thực tập sinh) trước những biến động nhanh chóng của thị trường lao động trong nước và quốc tế.

### Diễn giả

- **Anh Nguyễn Gia Hưng** – Solution Architect tại AWS Vietnam & Founder của FCAJ
- **Anh Tính Trương** – Platform Engineer tại Gotamic (Gotam X)
- **Anh Hải Anh** – Diễn giả trẻ về công nghệ tại Pacific Vietnam
- **Anh Nguyễn Hàn Thịnh** – DevOps Engineer
- **Chị Uyên & Chị Thảo** – Kỹ sư công nghệ, Quán quân Los H Hackathon (Dự án UTMMorpho)
- **Chuyên gia từ VPBank** – Chuyên gia doanh nghiệp trong lĩnh vực triển khai Platform và AI

### Nội dung nổi bật

#### Tổng quan thị trường & Xu hướng AI (Anh Nguyễn Gia Hưng)

- AI giúp giảm chi phí phát triển phần mềm đáng kể, khiến nhu cầu xây dựng các ứng dụng tăng vọt.  
- Sự xuất hiện của các quy trình vận hành mới như sửa lỗi code bằng AI (AI code fixing) và Platform Engineering để quản lý các hệ thống quy mô lớn.  

#### Tầm quan trọng của Context trong AI (Anh Tính Trương)

- Giải mã khái niệm "Context trong AI" và hiện tượng "Internet Buller" (việc quá phụ thuộc vào các nguồn tài nguyên ngẫu nhiên trên mạng làm nhiễu mô hình AI).  
- Để AI đưa ra kết quả chính xác, kỹ sư cần cung cấp bối cảnh cụ thể của dự án thay vì đưa vào các câu lệnh quá dài hoặc kiến thức chung chung.  

#### Amazon Q Apps & Agent (Anh Hải Anh)

- Demo chuyển đổi dữ liệu thô (như file Excel) thành các bảng phân tích dữ liệu tự động (BI analytics dashboard).  
- Xây dựng các AI Agent thông minh có khả năng tự động thực hiện hành động (như tóm tắt cuộc họp và tự động gửi email).  

#### Mô hình giá cố định AWS CloudFront Flat Rate (Anh Nguyễn Hàn Thịnh)

- Giải pháp kiến trúc bảo vệ doanh nghiệp khỏi hiện tượng "Bill Spikes" (chi phí tăng vọt) do tấn công DDoS hoặc lưu lượng truy cập tăng đột biến.  
- CloudFront giúp nén dữ liệu lên tới 82%, giảm tải CPU cho các máy chủ EC2 phía backend, đồng thời tăng cường bảo mật thông qua mTLS và VPC Origin.  

#### Hành trình 36 giờ chinh phục Hackathon (Chị Uyên & Chị Thảo)

- Chia sẻ hành trình căng thẳng khi xây dựng UTMMorpho (ứng dụng AI tạo giao diện HTML/CSS responsive trực tiếp từ hình ảnh và hỗ trợ chỉnh sửa kéo thả).  
- Vượt qua các thách thức trong điều kiện kiệt sức và giới hạn dung lượng token (token exhaustion).  

#### Hệ thống Multi-Agent cấp doanh nghiệp (Chuyên gia VPBank)

- Phân tích logic nghiệp vụ của mô hình chấm điểm tín dụng cho các công ty khởi nghiệp.  
- Demo cách điều phối một nhóm Agent chuyên biệt (Tài chính, Thị trường, Đội ngũ, Rủi ro) dưới sự quản lý của một Orchestrator trung tâm để xử lý các giao dịch phức tạp.  

### Bài học kinh nghiệm

#### Kỹ năng chuyên môn (Hard Skills)

- **Tối ưu hóa Context Window**: Thu gọn dung lượng dữ liệu đầu vào và tách các tác vụ khối monolithic phức tạp thành các nhánh xử lý riêng biệt.  
- **Nắm vững bản chất của LLM**: LLM về bản chất là mô hình xác suất; đặt temperature = 0 vẫn không đảm bảo kết quả giống nhau 100% giữa các lần chạy. Do đó, các dịch vụ tích hợp phía sau cần phải có quy trình kiểm thử liên tục.  
- **Quản trị chi phí Cloud**: Sử dụng các cơ chế giá có thể dự đoán (như CloudFront Flat Rate) giúp doanh nghiệp loại bỏ rủi ro ngân sách từ biến động lưu lượng hoặc đe dọa bảo mật.  
- **Xây dựng hệ thống Multi-Agent thực tế**: Nắm vững kiến trúc phân chia các vai trò Agent chuyên biệt dưới một node Orchestrator trung tâm để tự động hóa quy trình doanh nghiệp.  

#### Tư duy & Kỹ năng mềm (Soft Skills)

- **Chuyển dịch từ “Demo” sang “Production-Ready”**: Thị trường hiện tại chỉ đánh giá cao các kỹ sư có khả năng xây dựng hệ thống chạy thực tế (Production) giải quyết bài toán nghiệp vụ cụ thể, thay vì các bản demo học thuật cơ bản.  
- **Đặt Bảo mật & Tuân thủ lên hàng đầu**: Việc đưa AI vào môi trường doanh nghiệp khắt khe (như ngân hàng) đòi hỏi ranh giới bảo mật dữ liệu nghiêm ngặt và phải có nhật ký kiểm toán (Audit Trail).  
- **Tập trung vào điểm đau cốt lõi (Core Pain Point)**: Khi làm việc dưới áp lực thời gian ngắn, việc ôm đồm quá nhiều tính năng sẽ dẫn đến thiếu hụt tài nguyên. Chiến lược đúng đắn là giải quyết thật tốt một điểm đau thực tế duy nhất.  

### Trải nghiệm sự kiện

Tham dự **FCAJ Community Day 2026** đã mang lại những góc nhìn rất giá trị về hạ tầng điện toán đám mây hiện đại, việc tích hợp AI và các thực thi kỹ thuật trong môi trường thực tế.



> "Xây dựng một hệ thống không chỉ là làm cho nó hoạt động, mà là làm cho nó hoạt động an toàn, tin cậy và thực sự phục vụ đúng nhu cầu của người dùng."