# CSP (Content Security Policy)

## 1. Định nghĩa kỹ thuật

**CSP** là HTTP response header (`Content-Security-Policy`) khai báo **nguồn nào được phép** tải và thực thi từng loại tài nguyên trên trang: script, style, ảnh, font, iframe, kết nối `fetch`... Trình duyệt thực thi chính sách này và **chặn** mọi thứ vi phạm.

Mục đích chính: giảm thiệt hại của [XSS](./xss.md) và injection nói chung. Đây là lớp **phòng thủ chiều sâu**, không thay thế việc escape output.

## 2. Giải thích đơn giản

CSP là **danh sách khách được mời** của trang. Mỗi loại tài nguyên có danh sách riêng: "script chỉ nhận từ chính mình và `cdn.test`". Nếu kẻ tấn công chèn được `<script src="https://evil.test/x.js">`, trình duyệt thấy `evil.test` không có trong danh sách và từ chối chạy.

## 3. Các directive hay dùng

| Directive | Kiểm soát |
| --- | --- |
| `default-src` | Giá trị mặc định cho các loại không khai báo riêng |
| `script-src` | Nguồn JavaScript |
| `style-src` | Nguồn CSS |
| `img-src` | Ảnh |
| `connect-src` | `fetch`, XHR, WebSocket, SSE |
| `frame-src` | Nội dung iframe mà trang nhúng |
| `frame-ancestors` | Ai được nhúng **trang này** vào iframe (chống [Clickjacking](./clickjacking.md)) |
| `form-action` | Form được submit tới đâu |
| `base-uri` | Giá trị cho phép của thẻ `<base>` |
| `object-src` | `<object>`, `<embed>` (nên đặt `'none'`) |

Các giá trị nguồn: `'self'`, `'none'`, domain cụ thể, `https:`, `'nonce-...'`, `'sha256-...'`, `'strict-dynamic'`.

## 4. Ví dụ

### 4.1 Chính sách cơ bản

```
Content-Security-Policy:
  default-src 'self';
  script-src 'self' https://cdn.test;
  img-src 'self' data:;
  connect-src 'self' https://api.test;
  object-src 'none';
  base-uri 'self';
  frame-ancestors 'none'
```

### 4.2 Nonce cho inline script (khuyến nghị)

Cho phép inline script **chỉ khi có nonce** ngẫu nhiên, sinh mới ở mỗi response:

```
Content-Security-Policy: script-src 'nonce-r4nd0m123' 'strict-dynamic'; object-src 'none'; base-uri 'none'
```

```html
<script nonce="r4nd0m123">init();</script>
```

Kẻ tấn công chèn `<script>` mà không biết nonce thì không chạy được.

### 4.3 Chế độ Report-Only

Thử chính sách mà **không chặn**, chỉ báo cáo vi phạm:

```
Content-Security-Policy-Report-Only: default-src 'self'; report-to csp-endpoint
```

Nên triển khai theo thứ tự: Report-Only → sửa vi phạm → bật chặn thật.

## 5. Sai lầm thường gặp

- Dùng `'unsafe-inline'` / `'unsafe-eval'` trong `script-src`: gần như vô hiệu hoá khả năng chống XSS.
- Cho phép `*` hoặc `https:` quá rộng cho `script-src`.
- Whitelist CDN công cộng có JSONP hoặc thư viện cũ: kẻ tấn công mượn script ở đó để vượt CSP. Ưu tiên nonce/hash.
- Quên `object-src` và `base-uri`.
- Nonce dùng lại giữa các response (phải ngẫu nhiên mỗi lần).

## 6. Lưu ý

- CSP bổ trợ chứ không thay thế escape output và sanitize ở [XSS](./xss.md).
- Đặt header ở server hoặc ở edge ([Edge và CDN](../be/edge-cdn.md)). Xem thêm các header khác ở [Security Headers và HTTPS](./security-headers.md).
