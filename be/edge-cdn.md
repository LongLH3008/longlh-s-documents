# Edge và CDN

## 1. Định nghĩa kỹ thuật

**CDN (Content Delivery Network)** là mạng lưới các máy chủ phân tán theo địa lý, gọi là **PoP (Point of Presence)** hay **edge node**, lưu bản sao nội dung và phục vụ người dùng từ node **gần nhất**. Máy chủ gốc của bạn gọi là **origin**.

**Edge computing** mở rộng ý tưởng này: không chỉ lưu nội dung mà còn **chạy code ngay tại edge** (Cloudflare Workers, Lambda@Edge, Vercel Edge Functions) trước khi request chạm origin.

## 2. Giải thích đơn giản

Một kho hàng ở Hà Nội mà khách khắp thế giới đặt mua thì giao rất lâu và kho quá tải. Giải pháp: mở **kho nhỏ ở nhiều thành phố** (edge), mặt hàng phổ biến cất sẵn ở đó. Khách nhận hàng từ kho gần nhất. Kho nhỏ còn có **bảo vệ ở cổng**: kẻ gây rối bị chặn ngay, không bao giờ vào được kho chính.

```
Không CDN:   User (Brazil) ═════ 300ms ═════► Origin (Hà Nội)

Có CDN:      User (Brazil) ─ 20ms ─► Edge (São Paulo)
                                       ├─ HIT  → trả ngay, origin không biết gì
                                       └─ MISS → ═► Origin ═► lưu lại ở edge → trả user
```

![Trái: một server phục vụ mọi client. Phải: nhiều node CDN phân tán, mỗi client nối với node gần nhất](https://upload.wikimedia.org/wikipedia/commons/f/f9/NCDN_-_CDN.png)

*Trái: mô hình một server duy nhất. Phải: mô hình có CDN. Ảnh: [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:NCDN_-_CDN.png).*

## 3. Lợi ích

| Lợi ích | Giải thích |
|---|---|
| **Giảm độ trễ** | Dữ liệu đi quãng đường ngắn hơn |
| **Giảm tải origin** | Phần lớn request kết thúc ở edge |
| **Chịu tải đột biến** | Edge hấp thụ lượng truy cập lớn |
| **Bảo vệ origin** | Chặn tấn công và request rác trước khi tới server |
| **Tiết kiệm băng thông** | Origin gửi một lần, edge phát lại nhiều lần |

## 4. Chặn request xấu sớm "trước khi tới server"

Mỗi request chạm vào origin đều tốn CPU, kết nối, truy vấn DB. Nên **loại bỏ sớm nhất có thể**, ngay ở edge:

```
Request ─► [Edge]                              ─► [Origin] ─► [DB]
             ├─ WAF: chặn SQLi/XSS/payload độc
             ├─ DDoS protection, bot detection
             ├─ Rate limit theo IP / token
             ├─ Chặn theo quốc gia, ASN, blocklist
             ├─ Challenge (CAPTCHA) khi nghi ngờ
             └─ Cache: trả luôn nếu đã có
                  (chỉ request sạch, cần thiết mới đi tiếp)
```

| Cơ chế | Chặn được gì |
|---|---|
| **WAF** | Mẫu tấn công ứng dụng web phổ biến |
| **Rate limit tại edge** | Request dồn dập từ một nguồn ([Rate limit](./rate-limit.md)) |
| **Bot management** | Scraper, credential stuffing |
| **Cache** | Request lặp lại không cần origin |
| **Origin shield** | Gom các miss từ nhiều edge thành một request tới origin |

Edge không thay thế kiểm tra ở origin. Đây là lớp phòng thủ **thêm**, vì origin vẫn phải tự xác thực và phân quyền.

## 5. Điều khiển cache bằng header

```
Cache-Control: public, max-age=31536000, immutable   ← file tĩnh có hash trong tên
Cache-Control: s-maxage=60, stale-while-revalidate=30  ← cache ở CDN 60s, cho dùng bản cũ thêm 30s khi đang làm mới
Cache-Control: private, no-store                       ← dữ liệu cá nhân, tuyệt đối không cache chung
Vary: Accept-Encoding                                  ← khóa cache theo header (cẩn thận với Vary quá rộng)
```

- **Purge/invalidate:** xóa nội dung đã cache khi phát hành bản mới. Cách dễ hơn là **đặt tên file có hash** (`app.3f9a.js`) nên không bao giờ cần purge.
- **Negative caching:** cache cả `404` trong thời gian ngắn để chặn dò đường dẫn.
- **Cache key:** URL + vài header/query được chọn. Bỏ các tham số tracking (`utm_*`) để tăng hit ratio.

Nguyên lý chung của cache xem ở [Cache](./cache.md).

## 6. Rủi ro cần để ý

- **Cache nhầm nội dung cá nhân hóa:** response có `Set-Cookie` hoặc theo từng user mà bị cache chung sẽ rò rỉ dữ liệu cho người khác.
- **Cache thủng khi tham số lạ:** `?x=1`, `?x=2` mỗi cái là một key mới, kẻ tấn công cố tình làm miss để đánh vào origin.
- **IP thật bị che:** origin chỉ thấy IP của edge, phải đọc `X-Forwarded-For`/`CF-Connecting-IP` đúng cách, nếu không rate limit ở origin sẽ sai.
- **Origin vẫn để lộ:** nếu kẻ tấn công biết IP origin, họ đi vòng qua CDN. Hãy chỉ cho phép dải IP của CDN gọi vào origin.

## 7. Ghi chú FE và DB

> **FE:** CDN là nơi phục vụ JS/CSS/ảnh/font. Dùng file có hash, nén Brotli/gzip, định dạng ảnh hiện đại (WebP/AVIF), HTTP/2 hoặc HTTP/3. Ảnh tối ưu tại edge (resize theo thiết bị) giảm hàng loạt băng thông.

> **DB:** Request kết thúc ở edge là một truy vấn DB không bao giờ xảy ra. Đó là cách rẻ nhất để giảm tải đọc. API công khai ít thay đổi (danh sách, cấu hình) có thể cache ngắn hạn ở edge.

## 8. Liên quan

- [Rate limit](./rate-limit.md), [Cache](./cache.md)
- [Upload file lớn](./upload-heavy-file.md): upload vào edge gần nhất.
