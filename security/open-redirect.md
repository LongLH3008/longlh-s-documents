[← Mục lục](./index.md)

# Open Redirect

## 1. Định nghĩa kỹ thuật

**Open Redirect** là lỗ hổng khi ứng dụng **chuyển hướng người dùng tới một URL lấy từ input** (query, form, header) mà không kiểm tra đích. Kẻ tấn công tạo link thuộc domain **tin cậy** nhưng dẫn nạn nhân sang site độc hại.

```
https://shop.test/login?next=https://evil.test/fake-login
```

## 2. Giải thích đơn giản

Giống tấm biển chỉ đường chính thức của thành phố, nhưng ai cũng có thể sửa mũi tên. Người đi đường tin biển của thành phố nên đi theo, và bị dẫn tới một cửa hàng lừa đảo. Điểm nguy hiểm là link ban đầu **mang tên miền thật**, rất dễ qua mặt người dùng và bộ lọc email.

## 3. Ví dụ

```js
// Dễ bị tấn công: redirect sau đăng nhập
app.get('/login/done', (req, res) => {
  res.redirect(req.query.next);
});
```

Hậu quả:

- **Phishing**: dẫn tới trang đăng nhập giả giống hệt.
- Lấy cắp **OAuth code/token** khi `redirect_uri` kiểm tra lỏng.
- Vượt bộ lọc [SSRF](./ssrf.md) hoặc allowlist dựa trên domain.
- Phát tán malware dưới danh nghĩa domain uy tín.

### Các cách vượt kiểm tra sơ sài

| Payload | Lý do qua mặt |
| --- | --- |
| `//evil.test` | URL tương đối theo scheme (protocol-relative) |
| `https://shop.test.evil.test` | Chỉ kiểm tra `startsWith` hoặc `includes` |
| `https://shop.test@evil.test` | Phần trước `@` là userinfo |
| `/\evil.test`, `/%09/evil.test` | Trình duyệt chuẩn hoá thành `//evil.test` |
| `javascript:alert(1)` | Dẫn tới [XSS](./xss.md) |

## 4. Cách phòng chống

### 4.1 Không nhận URL tuỳ ý, dùng mã/ID

```js
const targets = { home: '/', orders: '/orders', profile: '/me' };
res.redirect(targets[req.query.to] ?? '/');
```

### 4.2 Chỉ cho phép đường dẫn nội bộ (relative path)

```js
function safeNext(next) {
  // chỉ nhận path bắt đầu bằng một dấu "/", không phải "//" hay "/\"
  if (typeof next === 'string' && /^\/(?![\/\\])/.test(next)) return next;
  return '/';
}
res.redirect(safeNext(req.query.next));
```

### 4.3 Nếu cần chuyển hướng ra ngoài

- **Allowlist** domain, parse bằng `new URL()` rồi so sánh `hostname` chính xác, không dùng `includes`/`startsWith`.
- Chỉ cho phép scheme `http`/`https`.
- Hiển thị **trang cảnh báo** "Bạn sắp rời khỏi site" trước khi đi.

### 4.4 OAuth / SSO

`redirect_uri` phải khớp **chính xác** với giá trị đã đăng ký, không dùng wildcard hay so sánh tiền tố.

## 5. Lưu ý

- Phía client cũng có biến thể: `location.href = params.get('next')`. Áp dụng cùng nguyên tắc kiểm tra.
- Gắn kèm [CSP](./csp.md) `form-action` và các [Security Headers](./security-headers.md) để giảm thiệt hại.
- Link xác thực dùng một lần nên đi kèm [Rate Limit](../be/rate-limit.md) và hết hạn nhanh.
