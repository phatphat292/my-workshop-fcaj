---
title : "WAF"
date : 2026-07-12
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---

#### Luồng dữ liệu

```
User Request
    │
    ▼
AWS WAF (gắn CloudFront)
    ├── AWSManagedRulesCommonRuleSet     — XSS, size bất thường, LFI...
    ├── AWSManagedRulesSQLiRuleSet       — SQL injection
    ├── AWSManagedRulesKnownBadInputsRuleSet — log4j, path traversal...
    ├── RateLimit — chặn IP gửi > 2000 request/5 phút
    └── PASS ──► CloudFront ──► ALB / S3
```

{{% notice warning %}}
Web ACL cho CloudFront **bắt buộc khai báo ở region us-east-1** (mặc dù CloudFront là dịch vụ global) — region trên Console luôn phải chuyển sang `us-east-1` trước khi tìm kiếm Web ACL.
{{% /notice %}}

#### 1. Kiểm tra Web ACL

Để kiểm chứng các thiết lập bảo mật:
1. Trước tiên, chuyển region trên Console sang **US East (N. Virginia) — us-east-1**.
2. Sau đó, mở **WAF & Shield console** → **Web ACLs** và truy cập vào `maison-edition-prod-waf`.
3. Tại tab **Rules**, đã có đủ 4 rule xếp đúng theo thứ tự ưu tiên (priority): `AWS-CommonRuleSet`, `AWS-SQLiRuleSet`, `AWS-KnownBadInputs`, và `RateLimit`.

![Rules](/images/5-Workshop/5.8-WAF/rule.png)

#### 2. Kiểm tra WAF đã gắn vào đúng CloudFront distribution

1. Chuyển sang tab **Associated AWS resources** trên trang Web ACL.
2. CloudFront distribution `E1RFSXKTT3PDNR` (FE storefront) đã được liên kết thành công.

![Associated AWS resources](/images/5-Workshop/5.8-WAF/associated.png)

#### 3. Test chặn XSS/SQLi

Để đảm bảo WAF chặn đúng các luồng nguy hiểm, các request kiểm thử được chạy bằng script (trên Git Bash hoặc WSL để tránh lỗi dấu nháy trên PowerShell):

```bash
echo "--- request binh thuong ---"
curl -s -o /dev/null -w 'HTTP=%{http_code}\n' 'https://d1tz40a6c8kj4v.cloudfront.net/api/v1/products?page=0'

echo "--- pattern SQLi ---"
curl -s -o /dev/null -w 'HTTP=%{http_code}\n' "https://d1tz40a6c8kj4v.cloudfront.net/api/v1/products?keyword=%27%20OR%20%271%27%3D%271"

echo "--- pattern XSS ---"
curl -s -o /dev/null -w 'HTTP=%{http_code}\n' "https://d1tz40a6c8kj4v.cloudfront.net/api/v1/products?keyword=%3Cscript%3Ealert(1)%3C%2Fscript%3E"
```

Khi chạy, kết quả hoàn toàn khớp với kỳ vọng: request bình thường trả về HTTP status `200`, trong khi 2 request chứa mã độc trả về `403` (đã bị WAF chặn từ lớp ngoài).

![Curl command results](/images/5-Workshop/5.8-WAF/curl.png)

#### 4. Xem chi tiết request bị chặn (Sampled requests)

Để xem log của các request độc hại:
1. Quay lại trang Web ACL và chuyển tới tab **Sampled requests**.
2. Vài phút sau khi test ở bước 3, làm mới trang (F5).
3. Các request độc hại vừa thử nghiệm hiển thị bị đánh dấu với **Action = BLOCK** kèm theo tên rule đã chặn nó (`AWS-SQLiRuleSet` hoặc `AWS-CommonRuleSet`).

![Sampled requests](/images/5-Workshop/5.8-WAF/log.png)
