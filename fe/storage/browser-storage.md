[← Mục lục](./index.md)

# Browser Storage và Credentials: Cookie, localStorage, sessionStorage

## 1. Định nghĩa kỹ thuật

**Browser storage** là các cơ chế trình duyệt cung cấp để web app lưu dữ liệu phía client. Ba cơ chế phổ biến nhất:

- [Cookie](./cookie.md): cặp key-value **tự động gửi kèm mỗi request** tới server cùng domain.
- [localStorage](./local-storage.md): lưu key-value (chuỗi) **lâu dài**, chỉ JS đọc ghi, **không tự gửi** lên server.
- [sessionStorage](./session-storage.md): giống localStorage nhưng **chỉ sống trong một tab** và mất khi đóng tab.

**Credentials** là thông tin dùng để chứng minh danh tính người dùng: session id, access token, JWT, refresh token, cookie xác thực, chứng chỉ client TLS, header `Authorization`. "Browser credentials" nghĩa là **nơi và cách trình duyệt giữ và gửi các thông tin này**.

## 2. Giải thích đơn giản

- **Cookie**: tấm **thẻ đeo trước ngực**. Mỗi lần bạn đi vào cửa (gửi request) bảo vệ tự nhìn thấy thẻ, bạn không cần đưa. Nếu thẻ có dán băng `HttpOnly` thì chính bạn (JS) cũng không tháo ra xem được.
- **localStorage**: **tủ cá nhân ở nhà**. Đồ để đó mãi (kể cả tắt máy mở lại), nhưng ai vào được nhà (script chạy trong trang) đều mở được. Muốn đưa gì cho bảo vệ thì tự mang ra.
- **sessionStorage**: **tờ giấy nhớ trên bàn làm việc của một tab**. Đóng tab là vứt. Tab khác không thấy.

## 3. So sánh

| Tiêu chí | Cookie | localStorage | sessionStorage |
| --- | --- | --- | --- |
| Dung lượng | ~4 KB / cookie | ~5 MB | ~5 MB |
| Thời gian sống | Theo `Expires`/`Max-Age`; nếu không đặt thì là **session cookie** (mất khi đóng trình duyệt) | **Vĩnh viễn** tới khi xóa | Hết khi **đóng tab** |
| Phạm vi chia sẻ | Mọi tab cùng domain (và theo `Path`, `Domain`) | Mọi tab cùng **origin** | **Một tab** (duplicate tab sẽ copy giá trị lúc đó) |
| Tự gửi lên server | **Có**, mỗi request | Không | Không |
| JS đọc được | Có, **trừ** khi `HttpOnly` | Luôn | Luôn |
| Server đặt được | Có (`Set-Cookie`) | Không | Không |
| Chống CSRF | Cần `SameSite`/CSRF token | Không bị CSRF tự động (vì không tự gửi) | Như localStorage |
| Chống XSS đánh cắp | Có nếu `HttpOnly` | **Không** | **Không** |
| API | `document.cookie` hoặc `Set-Cookie` | `localStorage.getItem/setItem` | `sessionStorage.getItem/setItem` |
| Dùng để lưu | Session id, token xác thực | Theme, ngôn ngữ, cache nhỏ | Dữ liệu form tạm, state của một tab |

Điểm khác biệt **dễ nhận biết nhất**:

- Thấy header `Cookie:` trong **mọi request** → cookie. Thấy `Set-Cookie:` trong response → server đang đặt cookie.
- Mở **tab mới** cùng site mà vẫn còn dữ liệu → cookie hoặc localStorage. Mất dữ liệu → sessionStorage.
- **Đóng hẳn trình duyệt** rồi mở lại mà vẫn còn → localStorage hoặc cookie có `Max-Age`. Mất → sessionStorage hoặc session cookie.
- Tab **Application** trong DevTools: mỗi loại có mục riêng (Cookies / Local Storage / Session Storage).

## 4. Phân biệt các nghĩa của "session"

"Session" dễ gây nhầm vì có hai nghĩa khác nhau:

| Thuật ngữ | Ý nghĩa |
| --- | --- |
| **Session cookie** | Cookie **không có** `Expires`/`Max-Age`, mất khi đóng trình duyệt (trình duyệt có thể khôi phục phiên và giữ lại) |
| **Persistent cookie** | Cookie có `Expires`/`Max-Age`, tồn tại qua nhiều lần mở trình duyệt |
| **sessionStorage** | Lưu theo **tab**, không liên quan tới session ở server |
| **Server session** | Dữ liệu phiên đăng nhập lưu ở server (Redis/DB), client chỉ giữ session id (thường trong cookie), xem [Opaque Token](../../be/auth-opaque-token-sliding-session.md) |

## 5. Lưu credentials ở đâu?

| Lựa chọn | Rủi ro chính | Đánh giá |
| --- | --- | --- |
| Cookie `HttpOnly; Secure; SameSite=Lax/Strict` | CSRF (đã giảm bằng `SameSite` + token) | **Khuyến nghị** cho session và token xác thực |
| localStorage | [XSS](../../security/xss.md) đọc được toàn bộ token | Tránh cho token nhạy cảm |
| sessionStorage | XSS đọc được, nhưng sống ngắn hơn | Chấp nhận được cho dữ liệu tạm, vẫn không lý tưởng cho token |
| Biến trong bộ nhớ JS (memory) | Mất khi reload; XSS vẫn dùng được trong lúc trang chạy | Hay dùng cho access token ngắn hạn, kèm refresh token trong cookie `HttpOnly` |

Nguyên tắc: **dữ liệu nào JS không cần đọc thì để trong cookie `HttpOnly`**. Chi tiết thuộc tính cookie xem [Secure Cookie](../../security/secure-cookie.md).

## 6. Ví dụ

```http
# Server đặt cookie phiên (JS không đọc được)
Set-Cookie: sid=9f3a1c; HttpOnly; Secure; SameSite=Lax; Path=/; Max-Age=1800
```

```js
// localStorage: lưu cấu hình giao diện, nằm lại sau khi tắt trình duyệt
localStorage.setItem('theme', 'dark');

// sessionStorage: chỉ sống trong tab này
sessionStorage.setItem('draft', JSON.stringify({ title: 'Nháp' }));

// Cookie không HttpOnly (JS đọc được, KHÔNG dùng cho token nhạy cảm)
document.cookie = 'lang=vi; Max-Age=31536000; Path=/; SameSite=Lax';
```

### Option `credentials` của fetch

Khi gọi API, quyết định có gửi cookie/credentials hay không bằng `credentials`:

```js
// 'same-origin' (mặc định): chỉ gửi cookie khi cùng origin
fetch('/api/me');

// 'include': gửi cookie cả khi cross-origin (cần CORS cho phép)
fetch('https://api.example.com/me', { credentials: 'include' });

// 'omit': không bao giờ gửi cookie
fetch('/api/public', { credentials: 'omit' });
```

Với `credentials: 'include'`, server phải trả `Access-Control-Allow-Credentials: true` và `Access-Control-Allow-Origin` là origin **cụ thể** (không được dùng `*`). Xem [CORS](../../security/cors.md).

## Xem thêm

- [Cookie](./cookie.md)
- [localStorage](./local-storage.md)
- [sessionStorage](./session-storage.md)
- [Secure Cookie](../../security/secure-cookie.md)
- [XSS](../../security/xss.md)
- [CSRF](../../security/csrf.md)
- [CORS](../../security/cors.md)
- [Auth bằng JWT và Token Version](../../be/auth-jwt-token-version.md)
- [Auth bằng Opaque Token và Sliding Session](../../be/auth-opaque-token-sliding-session.md)
- [SSR](../rendering/ssr.md): server đọc cookie để render theo từng user
