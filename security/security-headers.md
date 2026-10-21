[← Mục lục](./index.md)

# Security Headers và HTTPS

## 1. Định nghĩa kỹ thuật

**Security headers** là các HTTP response header chỉ thị cho trình duyệt **bật các cơ chế bảo vệ** khi hiển thị trang: bắt buộc HTTPS, chống nhúng iframe, chống đoán MIME, hạn chế quyền API... Cùng với **HTTPS (TLS)**, chúng tạo thành lớp bảo vệ nền tảng cho mọi web app.

**HTTPS** mã hoá và xác thực kết nối giữa trình duyệt và server, chống nghe lén và sửa đổi dữ liệu trên đường truyền (tấn công **MITM**).

## 2. Giải thích đơn giản

Server dán lên response những **tờ hướng dẫn an toàn** cho trình duyệt: "chỉ nói chuyện với tôi qua kênh có khoá", "đừng cho ai nhúng tôi vào khung", "đừng tự đoán loại file". Trình duyệt tuân thủ nên nhiều kiểu tấn công bị chặn từ đầu mà không cần sửa code ứng dụng.

## 3. Các header quan trọng

| Header | Mục đích |
| --- | --- |
| `Strict-Transport-Security` (HSTS) | Bắt trình duyệt luôn dùng HTTPS cho domain |
| `Content-Security-Policy` | Kiểm soát nguồn tài nguyên, giảm XSS. Xem [CSP](./csp.md) |
| `X-Content-Type-Options: nosniff` | Cấm trình duyệt đoán MIME type |
| `X-Frame-Options` / `frame-ancestors` | Chống [Clickjacking](./clickjacking.md) |
| `Referrer-Policy` | Kiểm soát lượng thông tin URL gửi qua header `Referer` |
| `Permissions-Policy` | Tắt các API không dùng (camera, mic, geolocation) |
| `Cross-Origin-Opener-Policy` (COOP) | Cô lập cửa sổ khỏi trang cross-origin mở ra nó |
| `Cross-Origin-Resource-Policy` (CORP) | Chặn site khác nhúng tài nguyên của bạn |
| `Cross-Origin-Embedder-Policy` (COEP) | Yêu cầu tài nguyên nhúng phải cho phép rõ ràng |

### 3.1 HSTS

```
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

Sau lần truy cập HTTPS đầu tiên, trình duyệt tự đổi mọi `http://` thành `https://` trong 1 năm, ngăn **SSL stripping**. `preload` đưa domain vào danh sách cứng của trình duyệt (cần cân nhắc kỹ vì khó gỡ).

### 3.2 `nosniff`

```
X-Content-Type-Options: nosniff
```

Ngăn file người dùng upload (ví dụ `.txt` chứa JS) bị trình duyệt đoán là script và thực thi. Liên quan đến [Upload File Lớn](../be/upload-heavy-file.md): luôn trả đúng `Content-Type`.

### 3.3 Referrer-Policy

```
Referrer-Policy: strict-origin-when-cross-origin
```

Tránh rò đường dẫn chứa token hoặc thông tin nhạy cảm sang site khác.

## 4. Cấu hình ví dụ

```js
// Express với helmet
import helmet from 'helmet';
app.use(helmet());
```

Hoặc đặt ở reverse proxy / CDN, xem [Edge và CDN](../be/edge-cdn.md):

```nginx
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
add_header X-Content-Type-Options "nosniff" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Permissions-Policy "camera=(), microphone=(), geolocation=()" always;
```

## 5. Checklist HTTPS

- Chuyển hướng toàn bộ HTTP sang HTTPS (301) và bật HSTS.
- Dùng TLS 1.2 trở lên, tắt phiên bản và cipher cũ.
- Chứng chỉ hợp lệ, tự động gia hạn (ví dụ Let's Encrypt).
- Tránh **mixed content** (trang HTTPS nhưng tải tài nguyên HTTP).
- Cookie phải có `Secure`. Xem [Secure Cookie](./secure-cookie.md).

## 6. Lưu ý

- Header là lớp **phòng thủ chiều sâu**, không thay thế việc sửa lỗ hổng trong code như [XSS](./xss.md) hay [SQL Injection](./sql-injection.md).
- Kiểm tra cấu hình bằng công cụ như securityheaders.com hoặc Mozilla Observatory.
- Cấu hình [CORS](./cors.md) cũng là header nhưng có mục đích riêng (nới SOP chứ không siết thêm).
