---
title : "ElastiCache Redis"
date : 2026-07-12
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Luồng dữ liệu

```
Ứng dụng ──► Redis HIT  ──► trả ngay, không chạm RDS
        └──► Redis MISS ──► RDS ──► ghi vào Redis (TTL)
```

Hệ thống cache ba loại dữ liệu: `productDetail` (TTL 90 giây — chứa thông tin tồn kho nên TTL ngắn), `categories` và `brands` (TTL 10 phút). Mọi thao tác từ phía admin như tạo/sửa/xóa sản phẩm, danh mục hoặc brand đều xóa cache tương ứng ngay lập tức, không đợi hết TTL.

#### 1. Kiểm tra cluster Redis

Để xác nhận hệ thống cache đang hoạt động bình thường:
1. Trên **ElastiCache console**, truy cập phần **Redis caches**.
2. Cluster `fashion-shop-prod-redis` có **Status** là `available`, **Node type** là `cache.t3.micro`.
3. Ghi lại thông tin **Primary endpoint** để phục vụ bước kiểm tra dữ liệu thực tế bên dưới.

![Endpoint](/images/5-Workshop/5.4-ElastiCache-Redis/redis_info.png)

#### 2. Kiểm tra Security Group

Để đảm bảo tính bảo mật, tường lửa nội bộ được kiểm tra:
1. Tại tab **Security groups** trên trang cluster, mở SG `fashion-shop-prod-redis-sg`.
2. Trong tab **Inbound rules**, chỉ có đúng 1 rule cho port `6379`, với nguồn truy cập (source) được trỏ đích danh tới Security Group của ứng dụng. Redis hoàn toàn không mở public ra `0.0.0.0/0`.

![Redis SG](/images/5-Workshop/5.4-ElastiCache-Redis/sg.png)

#### 3. Test cache — xem key thật

Do Redis nằm sâu trong private subnet và không thể truy cập từ internet, việc kết nối để chạy `redis-cli` từ trong network đi qua **Session Manager** trên instance của ứng dụng (shell trên trình duyệt không cần SSH key hay mở cổng):

1. Đầu tiên, mở storefront và tải vài sản phẩm để hệ thống ghi dữ liệu vào cache.
2. Từ **Systems Manager** → **Session Manager**, chọn **Start session** và truy cập vào một trong các instance `fashion-shop-prod-app`.
3. Trong shell, dùng docker để chạy lệnh `redis-cli` trỏ tới endpoint đã lấy ở trên:

```bash
docker run --rm redis:7-alpine redis-cli \
  -h fashion-shop-prod-redis.voe1xs.ng.0001.apse1.cache.amazonaws.com \
  --scan --pattern 'fashion-shop:*'
```

Kết quả trả về cho thấy các key tồn tại thực tế trên Redis như `fashion-shop:productDetail::...`, `fashion-shop:categories::...`.

![redis_key](/images/5-Workshop/5.4-ElastiCache-Redis/redis_key.png)

#### 4. Xem chỉ số cache hit qua CloudWatch

Cuối cùng, để đo hiệu quả cache:
1. Tại **ElastiCache console** → chọn cluster → tab **Metrics** (có thể mở chi tiết sang CloudWatch).
2. Theo dõi hai biểu đồ **CacheHits** và **CacheMisses**. Sau khi refresh trang storefront vài lần, lượng CacheHits tăng lên rõ rệt.

![hit](/images/5-Workshop/5.4-ElastiCache-Redis/hit.png)