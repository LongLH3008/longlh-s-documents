[← Mục lục](./index.md)

# CSRF (Cross-Site Request Forgery)

## 1. Định nghĩa kỹ thuật

**CSRF** là kiểu tấn công khiến trình duyệt của nạn nhân **gửi một request có chủ đích của kẻ tấn công** tới site mà nạn nhân đang đăng nhập. Vì trình duyệt **tự động đính kèm cookie** (session) vào mọi request tới domain đó, server không phân biệt được request do người dùng chủ động hay bị lừa gửi.

Điều kiện để tấn công thành công:

1. Xác thực dựa trên thứ trình duyệt **tự gửi** (cookie, HTTP Basic).
2. Có một hành động làm thay đổi trạng thái (đổi mật khẩu, chuyển tiền).
3. Kẻ tấn công **đoán trước được** toàn bộ tham số của request.

## 2. Giải thích đơn giản

Bạn đang đăng nhập ngân hàng ở một tab. Ở tab khác bạn mở một trang lạ, trang đó âm thầm gửi lệnh "chuyển 10 triệu cho X" tới ngân hàng. Trình duyệt thấy request đi tới ngân hàng nên **tự gắn cookie đăng nhập** của bạn. Ngân hàng thấy cookie hợp lệ và thực hiện lệnh.

Kẻ tấn công **không đọc được** phản hồi, chỉ **ép được hành động**. Vì vậy CSRF nhắm vào các thao tác ghi (POST/PUT/DELETE, hoặc GET bị dùng sai).

## 3. Ví dụ tấn công

```html
<!-- Trang evil.test, nạn nhân chỉ cần mở trang -->
<form action="https://bank.test/transfer" method="POST" id="f">
  <input type="hidden" name="to" value="attacker">
  <input type="hidden" name="amount" value="10000000">
</form>
<script>document.getElementById('f').submit();</script>
```

Biến thể với GET (do API thiết kế sai, dùng GET để đổi dữ liệu):

```html
<img src="https://bank.test/transfer?to=attacker&amount=10000000">
```

## 4. Cách phòng chống

### 4.1 Cookie `SameSite`

Thuộc tính `SameSite` bảo trình duyệt **không gửi cookie** trong request xuất phát từ site khác.

- `Lax` (mặc định ở trình duyệt hiện đại): chặn cookie ở POST cross-site, vẫn cho phép khi điều hướng GET top-level.
- `Strict`: chặn mọi trường hợp cross-site.
- `None; Secure`: luôn gửi (cần cho nhúng bên thứ ba, mất bảo vệ).

Chi tiết ở [Secure Cookie](./secure-cookie.md).

### 4.2 CSRF token

Server sinh một token **ngẫu nhiên, gắn với session**, nhúng vào form/header. Request thay đổi trạng thái phải kèm token đúng. Trang của kẻ tấn công không đọc được token (bị chặn bởi Same-Origin Policy) nên không giả mạo được.

```html
<form method="POST" action="/transfer">
  <input type="hidden" name="_csrf" value="r4nd0m-token">
</form>
```

```js
// SPA: gửi qua header
fetch('/api/transfer', {
  method: 'POST',
  headers: { 'X-CSRF-Token': token },
  credentials: 'include',
});
```

### 4.3 Double Submit Cookie

Không cần lưu token ở server: đặt token vào cookie **và** yêu cầu client gửi lại cùng giá trị trong header/body. Server chỉ so sánh hai giá trị. Nên ký (HMAC) token để tránh bị ghi đè từ subdomain.

### 4.4 Kiểm tra `Origin` / `Referer`

Với request thay đổi trạng thái, server kiểm tra header `Origin` (hoặc `Referer`) có nằm trong danh sách origin tin cậy. Là lớp bổ trợ tốt, đặc biệt cho API.

### 4.5 Khác

- **Không dùng GET** để thay đổi dữ liệu.
- Yêu cầu `Content-Type: application/json` kèm kiểm tra: form HTML thường không gửi được JSON, nhưng cần CORS cấu hình đúng. Xem [CORS](./cors.md).
- Hành động nhạy cảm (đổi mật khẩu, chuyển tiền) nên **xác nhận lại mật khẩu/OTP**.
- Xác thực bằng header `Authorization: Bearer` (không phải cookie) thì miễn nhiễm CSRF, nhưng phải lưu token cẩn thận vì rủi ro [XSS](./xss.md). Xem [Auth JWT](../be/auth-jwt-token-version.md).

## 5. Phân biệt CSRF và XSS

| | CSRF | XSS |
| --- | --- | --- |
| Lợi dụng | Niềm tin của **server** vào trình duyệt | Niềm tin của **trình duyệt** vào site |
| Kẻ tấn công | Chạy từ site khác | Chạy mã ngay trong site đích |
| Đọc được dữ liệu | Không | Có |
| Chống bằng | SameSite, CSRF token | Escape output, CSP |

Nếu có XSS, kẻ tấn công đọc được CSRF token nên các biện pháp CSRF vô hiệu. Hai lỗi cần được chặn song song.
