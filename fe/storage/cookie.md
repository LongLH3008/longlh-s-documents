[← Mục lục](./index.md)

# Cookie

## 1. Định nghĩa kỹ thuật

**Cookie** là một đơn vị dữ liệu nhỏ (khoảng 4 KB) do server đặt qua header `Set-Cookie` (hoặc JS qua `document.cookie`). Trình duyệt lưu và **tự động gửi lại qua header `Cookie`** trong mọi request tiếp theo khớp `Domain` và `Path`. Cookie là cách chuẩn để giữ trạng thái trên giao thức HTTP vốn **stateless**.

## 2. Giải thích đơn giản

Giống **thẻ thành viên**. Lần đầu bạn đến quán, nhân viên (server) phát thẻ. Từ lần sau, mỗi khi bước vào, bạn tự động đưa thẻ ra và quán nhận ra bạn ngay mà không cần hỏi lại.

## 3. Luồng hoạt động

```
Browser                              Server
  │ POST /login ───────────────────► │
  │ ◄── Set-Cookie: sid=abc; ... ─── │
  │ (trình duyệt lưu cookie)         │
  │ GET /profile                     │
  │ Cookie: sid=abc ───────────────► │  server tra sid → biết là ai
  │ ◄──────────────── 200 ────────── │
```

## 4. Phân loại và đặc điểm

| Loại | Đặc điểm |
| --- | --- |
| **Session cookie** | Không có `Expires`/`Max-Age`, mất khi đóng trình duyệt |
| **Persistent cookie** | Có `Expires`/`Max-Age`, tồn tại qua nhiều lần mở |
| **First-party** | Thuộc domain của trang đang mở |
| **Third-party** | Thuộc domain khác (quảng cáo, tracking), ngày càng bị trình duyệt chặn |

Điểm dễ nhận biết: cookie là cơ chế **duy nhất** trong ba loại storage mà **server đặt được** và **tự đi theo request**.

Thuộc tính `HttpOnly`, `Secure`, `SameSite`, `Domain`, `Path` được giải thích chi tiết ở [Secure Cookie](../../security/secure-cookie.md).

## 5. Ví dụ

```http
Set-Cookie: sid=9f3a1c; HttpOnly; Secure; SameSite=Lax; Path=/; Max-Age=1800
```

```js
// Đọc/ghi từ JS (chỉ cookie không HttpOnly)
document.cookie = 'lang=vi; Max-Age=31536000; Path=/';
console.log(document.cookie); // "lang=vi"  (không thấy cookie HttpOnly)

// Xóa cookie: đặt Max-Age=0 với đúng Path/Domain đã dùng khi tạo
document.cookie = 'lang=; Max-Age=0; Path=/';
```

```ts
// Next.js (server): đọc cookie để render theo user
import { cookies } from 'next/headers';
const sid = (await cookies()).get('sid')?.value;
```

## 6. Lưu ý

- Cookie đi theo **mọi request** (kể cả ảnh, CSS cùng domain) nên đặt nhiều hoặc quá lớn sẽ làm chậm. Dữ liệu lớn không cần server thì dùng [localStorage](./local-storage.md).
- Cookie nhạy cảm phải có `HttpOnly; Secure; SameSite`.
- Cross-origin `fetch` chỉ gửi cookie khi có `credentials: 'include'` và CORS cho phép, xem [So sánh Browser Storage](./browser-storage.md).
- Cookie có thể bị **CSRF** (vì tự gửi), xem [CSRF](../../security/csrf.md).

## Xem thêm

- [So sánh Browser Storage và Credentials](./browser-storage.md)
- [localStorage](./local-storage.md)
- [sessionStorage](./session-storage.md)
- [Secure Cookie](../../security/secure-cookie.md)
- [CSRF](../../security/csrf.md)
- [Auth bằng Opaque Token và Sliding Session](../../be/auth-opaque-token-sliding-session.md)
