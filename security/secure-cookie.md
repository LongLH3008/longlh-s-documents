# Secure Cookie

## 1. Định nghĩa kỹ thuật

**Cookie** là cặp key-value server gửi qua header `Set-Cookie`, trình duyệt lưu và **tự động gửi lại** trong các request tiếp theo tới cùng domain. Các **thuộc tính (attribute)** của cookie quyết định cookie được gửi khi nào, tới đâu và JS có đọc được hay không. Cấu hình đúng các thuộc tính này là tuyến phòng thủ quan trọng cho session chống [XSS](./xss.md) và [CSRF](./csrf.md).

```
Set-Cookie: sid=abc123; HttpOnly; Secure; SameSite=Lax; Path=/; Max-Age=3600
```

## 2. Giải thích đơn giản

Cookie session giống **thẻ ra vào** của bạn. Các thuộc tính là quy định sử dụng thẻ:

- `HttpOnly`: thẻ để trong ví, **chương trình JS không được rút ra xem**.
- `Secure`: chỉ đưa thẻ khi đi qua **đường có khoá** (HTTPS).
- `SameSite`: **không đưa thẻ** khi người lạ ở site khác yêu cầu hộ.
- `Max-Age`: thẻ **hết hạn** sau một thời gian.

## 3. Các thuộc tính

| Thuộc tính | Tác dụng | Chống |
| --- | --- | --- |
| `HttpOnly` | `document.cookie` không đọc được | Trộm session qua XSS |
| `Secure` | Chỉ gửi qua HTTPS | Nghe lén trên mạng (MITM) |
| `SameSite=Strict/Lax/None` | Giới hạn gửi cookie trong request cross-site | CSRF |
| `Domain` | Domain nhận cookie (có thể gồm subdomain) | Rò cookie sang subdomain không tin cậy |
| `Path` | Đường dẫn nhận cookie | Phạm vi nhỏ hơn (không phải biên bảo mật thật) |
| `Max-Age` / `Expires` | Thời hạn sống | Session sống quá lâu |
| Prefix `__Host-` | Buộc `Secure`, `Path=/`, không có `Domain` | Ghi đè cookie từ subdomain |

### 3.1 SameSite chi tiết

- **Strict**: cookie chỉ gửi khi request xuất phát từ cùng site. Bảo mật cao nhất, nhưng bấm link từ email vào site sẽ như chưa đăng nhập.
- **Lax**: gửi thêm khi điều hướng top-level bằng GET (bấm link). Chặn POST, iframe, fetch cross-site. Cân bằng tốt, là mặc định ở trình duyệt hiện đại.
- **None**: luôn gửi, bắt buộc kèm `Secure`. Chỉ dùng khi thật sự cần cross-site (widget nhúng, SSO).

"Same-site" tính theo **registrable domain** (`a.example.com` và `b.example.com` là cùng site), khác với same-origin.

## 4. Ví dụ

```js
// Express
res.cookie('sid', sessionId, {
  httpOnly: true,
  secure: true,
  sameSite: 'lax',
  maxAge: 60 * 60 * 1000,
  path: '/',
});
```

Dùng prefix để chống ghi đè:

```
Set-Cookie: __Host-sid=abc123; Secure; HttpOnly; SameSite=Lax; Path=/
```

## 5. Cách làm đúng

- Cookie chứa session/token luôn có `HttpOnly; Secure; SameSite=Lax` (hoặc `Strict`).
- Session id phải **ngẫu nhiên, đủ dài**, sinh bằng CSPRNG. Xem [Auth Opaque Token](../be/auth-opaque-token-sliding-session.md).
- **Đổi session id sau khi đăng nhập** để chống session fixation, và huỷ session phía server khi đăng xuất.
- Đặt thời hạn hợp lý, dùng sliding session có giới hạn tuyệt đối.
- Không nhét dữ liệu nhạy cảm vào cookie (nếu cần, dùng JWT có ký, xem [Auth JWT](../be/auth-jwt-token-version.md)), nhớ giới hạn khoảng 4KB mỗi cookie.
- Bật [HTTPS và HSTS](./security-headers.md) để thuộc tính `Secure` có ý nghĩa.

## 6. So sánh cookie và localStorage

| | Cookie `HttpOnly` | `localStorage` |
| --- | --- | --- |
| XSS đọc được | Không | Có |
| Tự gửi theo request | Có (cần chống CSRF) | Không (JS tự gắn header) |
| Phù hợp lưu session | Có | Rủi ro cao |

Nếu dùng cookie xuyên origin, cần cấu hình đúng [CORS](./cors.md) (`credentials`).
