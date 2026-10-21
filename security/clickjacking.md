# Clickjacking

## 1. Định nghĩa kỹ thuật

**Clickjacking** (UI redress attack) là kiểu tấn công **nhúng trang đích vào `<iframe>` trong suốt** đặt chồng lên một giao diện giả. Người dùng tưởng đang bấm vào nút của trang giả, thực chất click rơi vào nút trên trang thật (đang đăng nhập) và kích hoạt hành động ngoài ý muốn.

## 2. Giải thích đơn giản

Giống việc kẻ xấu **dán một tờ giấy trong suốt** lên nút "Nhận quà" nhưng bên dưới tờ giấy là nút "Xoá tài khoản". Bạn nhìn thấy chữ "Nhận quà" và bấm, nhưng ngón tay chạm vào nút bên dưới.

## 3. Ví dụ tấn công

```html
<!-- evil.test -->
<style>
  iframe { position: absolute; inset: 0; opacity: 0.0001; z-index: 2; }
  .bait  { position: absolute; top: 200px; left: 300px; z-index: 1; }
</style>
<button class="bait">Nhận quà miễn phí</button>
<iframe src="https://bank.test/settings/delete-account"></iframe>
```

Nạn nhân đã đăng nhập `bank.test` nên iframe mang theo cookie hợp lệ. Các biến thể: likejacking (ép like/follow), cursorjacking, kéo thả dữ liệu (drag-and-drop).

## 4. Cách phòng chống

### 4.1 `frame-ancestors` trong CSP (chuẩn hiện đại)

Chỉ định ai được nhúng trang của bạn:

```
Content-Security-Policy: frame-ancestors 'none'        # cấm hoàn toàn
Content-Security-Policy: frame-ancestors 'self'         # chỉ cùng origin
Content-Security-Policy: frame-ancestors https://partner.test
```

Xem thêm [CSP](./csp.md).

### 4.2 `X-Frame-Options` (cũ, vẫn nên gửi kèm để tương thích)

```
X-Frame-Options: DENY          # cấm
X-Frame-Options: SAMEORIGIN    # chỉ cùng origin
```

Khi cả hai cùng có, trình duyệt hiện đại ưu tiên `frame-ancestors`.

### 4.3 Cookie `SameSite`

Cookie `SameSite=Lax/Strict` không được gửi trong iframe cross-site, nên trang nhúng chỉ thấy trạng thái chưa đăng nhập. Xem [Secure Cookie](./secure-cookie.md).

### 4.4 Xác nhận cho hành động nguy hiểm

Hành động huỷ hoại (xoá tài khoản, chuyển tiền) nên yêu cầu nhập lại mật khẩu, OTP hoặc CAPTCHA; một cú click đơn lẻ không nên đủ để thực hiện.

### 4.5 Frame-busting bằng JS (không nên dựa vào)

```js
if (window.top !== window.self) window.top.location = window.location;
```

Có thể bị vô hiệu bằng `sandbox` của iframe. Chỉ dùng header, không dùng JS làm biện pháp chính.

## 5. Lưu ý

- Clickjacking khác [CSRF](./csrf.md): ở đây request do **chính người dùng bấm** trên trang thật, nên CSRF token không cản được.
- Nếu trang cần được nhúng hợp lệ (widget, thanh toán), hãy allowlist origin cụ thể thay vì tắt bảo vệ.
- Cấu hình header tập trung ở [Security Headers và HTTPS](./security-headers.md).
