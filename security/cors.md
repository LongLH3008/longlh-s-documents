[← Mục lục](./index.md)

# CORS (Cross-Origin Resource Sharing)

## 1. Định nghĩa kỹ thuật

**CORS** là cơ chế dựa trên HTTP header cho phép server khai báo **origin nào được phép đọc response** của nó từ trình duyệt. Nó nới lỏng **Same-Origin Policy (SOP)**, chính sách mặc định chặn JS ở origin này đọc dữ liệu của origin khác.

**Origin** = `scheme` + `host` + `port`. `https://a.test`, `https://api.a.test` và `http://a.test` là **ba origin khác nhau**.

## 2. Giải thích đơn giản

SOP giống quy định "người lạ không được đọc thư của bạn". CORS là **giấy ủy quyền** của chủ nhà: "người ở `https://app.test` được phép đọc". Trình duyệt kiểm tra giấy này, không có hoặc không khớp thì **không đưa kết quả cho JS**.

Điểm hay bị hiểu nhầm:

- CORS do **trình duyệt thực thi**, không phải server. Postman, curl, server khác gọi API vẫn bình thường.
- CORS **không chặn request được gửi đi**, chỉ chặn JS **đọc response**. Request đơn giản vẫn tới server (nên không thay thế được bảo vệ [CSRF](./csrf.md)).
- CORS không phải lớp xác thực. Nó chỉ trả lời "origin nào được đọc".

## 3. Cơ chế hoạt động

### 3.1 Simple request

Với `GET`/`POST` đơn giản (content-type `text/plain`, form...), trình duyệt gửi luôn kèm `Origin`. Server trả header cho phép:

```
Origin: https://app.test                          (request)
Access-Control-Allow-Origin: https://app.test     (response)
```

### 3.2 Preflight request

Với request "không đơn giản" (method `PUT`/`DELETE`, header tuỳ chỉnh như `Authorization`, `Content-Type: application/json`), trình duyệt gửi trước một `OPTIONS` để hỏi:

```
OPTIONS /api/orders
Origin: https://app.test
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: content-type, authorization
```

Server trả lời:

```
Access-Control-Allow-Origin: https://app.test
Access-Control-Allow-Methods: GET, PUT, DELETE
Access-Control-Allow-Headers: content-type, authorization
Access-Control-Max-Age: 600
```

Chỉ khi preflight hợp lệ, request thật mới được gửi. `Max-Age` cho phép cache kết quả để giảm số lần preflight.

### 3.3 Request kèm credentials

Muốn gửi cookie cross-origin, client phải đặt `credentials: 'include'` và server phải trả `Access-Control-Allow-Credentials: true`. Khi đó `Allow-Origin` **không được là `*`**, phải là origin cụ thể.

```js
fetch('https://api.test/me', { credentials: 'include' });
```

## 4. Cấu hình sai thường gặp

| Sai lầm | Hậu quả |
| --- | --- |
| `Allow-Origin: *` cho API cần đăng nhập | Dữ liệu mở cho mọi site (nếu không dùng credentials) |
| Phản chiếu nguyên `Origin` của request vào `Allow-Origin` kèm `Allow-Credentials: true` | Site nào cũng đọc được dữ liệu của người dùng đã đăng nhập |
| Kiểm tra origin bằng `endsWith('.test')` hoặc `includes` | Bị bypass bằng `evil-test`, `test.evil.com` |
| Cho phép origin `null` | Bị khai thác qua iframe sandbox, file local |
| Tin CORS thay cho bảo vệ CSRF | Request vẫn được gửi và có tác dụng phụ |

## 5. Cách làm đúng

```js
// Express: allowlist origin chính xác
const allowed = new Set(['https://app.test', 'https://admin.test']);

app.use((req, res, next) => {
  const origin = req.headers.origin;
  if (origin && allowed.has(origin)) {
    res.setHeader('Access-Control-Allow-Origin', origin);
    res.setHeader('Access-Control-Allow-Credentials', 'true');
    res.setHeader('Vary', 'Origin'); // tránh cache nhầm giữa các origin
  }
  if (req.method === 'OPTIONS') {
    res.setHeader('Access-Control-Allow-Methods', 'GET,POST,PUT,DELETE');
    res.setHeader('Access-Control-Allow-Headers', 'content-type,authorization');
    return res.sendStatus(204);
  }
  next();
});
```

Nguyên tắc:

- **Allowlist** origin cụ thể, so sánh chính xác toàn chuỗi.
- Luôn thêm `Vary: Origin` khi giá trị thay đổi theo origin, đặc biệt khi response đi qua cache/CDN ([Edge và CDN](../be/edge-cdn.md)).
- Chỉ mở method và header cần thiết, giữ `Max-Age` hợp lý.
- Dùng `*` chỉ cho API công khai, không cookie.
- Dev: dùng proxy của dev server thay vì mở CORS toàn bộ.
- Kết hợp [Secure Cookie](./secure-cookie.md) (`SameSite`) và [CSRF token](./csrf.md).

## 6. Liên hệ

Lỗi "blocked by CORS policy" thường gặp khi gọi [REST API](../fe/rest-api.md) từ frontend khác origin. Cách sửa đúng là cấu hình **ở server**, không phải ở client.
