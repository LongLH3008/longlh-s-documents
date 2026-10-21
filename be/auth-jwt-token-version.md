# Auth bằng JWT và Token Version

## 1. Định nghĩa kỹ thuật

**JSON Web Token (JWT)** (RFC 7519) là một chuẩn mở định nghĩa cách biểu diễn các **claims** (khẳng định về một chủ thể) dưới dạng chuỗi compact, an toàn khi đặt trong URL, được **ký số (JWS)** hoặc mã hóa (JWE) để bên nhận kiểm chứng được tính toàn vẹn.

Một JWT gồm 3 phần nối bằng dấu `.`:

```
eyJhbGciOiJIUzI1NiJ9 . eyJzdWIiOiI0MiIsImV4cCI6MTc... . 3kV9x...
└── header ──────────┘   └── payload (claims) ────────┘   └ signature ┘
 {"alg":"HS256"}          {"sub":"42","exp":1760000000}     HMAC/RSA/ECDSA
```

| Claim | Ý nghĩa |
|---|---|
| `sub` | Subject: ID người dùng |
| `exp` | Thời điểm hết hạn (Unix time) |
| `iat` | Thời điểm phát hành |
| `iss` / `aud` | Bên phát hành / bên được phép nhận |
| `jti` | ID duy nhất của token |

## 2. Giải thích đơn giản

JWT giống như **vé xem phim có đóng dấu của rạp**. Nhân viên soát vé chỉ cần nhìn con dấu là biết vé thật, không cần gọi về quầy tra sổ. Nhờ vậy server **không phải lưu session**, gọi là **stateless**.

```
 Client                         Server
   │  POST /login (user, pass)    │
   │ ───────────────────────────► │  kiểm tra mật khẩu
   │  ◄─────── JWT (đã ký) ────── │  ký bằng secret/private key
   │                              │
   │  GET /me  + Authorization:   │
   │  Bearer <JWT>                │
   │ ───────────────────────────► │  verify chữ ký + exp, KHÔNG tra DB
   │  ◄───────── 200 ──────────── │
```

> **Lưu ý:** payload chỉ được **mã hóa base64**, không bí mật. Ai cũng đọc được, nên không để mật khẩu hay dữ liệu nhạy cảm trong JWT.

## 3. Vấn đề: không thu hồi được

Vì server không nhớ gì, một JWT còn hạn thì **luôn hợp lệ**, kể cả khi:

- User đổi mật khẩu.
- User bấm "đăng xuất khỏi mọi thiết bị".
- Admin khóa tài khoản.
- Token bị lộ.

Có 3 cách giải quyết:

| Cách | Ưu | Nhược |
|---|---|---|
| Access token sống rất ngắn (5–15 phút) + refresh token | Đơn giản | Vẫn còn khoảng hở vài phút |
| Blacklist `jti` đã thu hồi | Thu hồi từng token | Phải tra store mỗi request, mất tính stateless |
| **Token version** | Thu hồi toàn bộ token của 1 user chỉ bằng 1 con số | Cần 1 lần tra (nên cache) mỗi request |

## 4. Token version

Mỗi user có một cột số nguyên `token_version` trong DB. Mỗi JWT mang theo claim `ver` là giá trị tại thời điểm phát hành.

```
users:  id=42, token_version = 3

Phát hành JWT:  { sub: 42, ver: 3, exp: ... }

Mỗi request:
   payload.ver (3)  ==  users[42].token_version (3) ?
        ├─ bằng nhau → cho qua
        └─ khác nhau → 401 (token đã bị thu hồi)

Khi đổi mật khẩu / logout-all:
   UPDATE users SET token_version = token_version + 1 WHERE id = 42;
   → mọi JWT cũ mang ver=3 lập tức vô hiệu
```

```ts
// phát hành
const token = jwt.sign({ sub: user.id, ver: user.tokenVersion }, SECRET, { expiresIn: '15m' });

// middleware
const payload = jwt.verify(token, SECRET);
const currentVer = await cache.get(`tv:${payload.sub}`) ?? await db.getTokenVersion(payload.sub);
if (payload.ver !== currentVer) throw new UnauthorizedError();
```

**Đánh đổi:** bạn đã đổi một chút tính stateless để lấy khả năng thu hồi. Để giảm tải, lưu `token_version` trong **cache** (Redis, xem [cache.md](./cache.md)) và xóa key khi version thay đổi.

## 5. Access token + Refresh token

```
Access token  : sống ngắn (15 phút), gửi mỗi request
Refresh token : sống dài (7–30 ngày), chỉ gửi tới endpoint /refresh

 access hết hạn ──► gửi refresh token ──► nhận cặp token mới
```

Nên dùng **refresh token rotation**: mỗi lần refresh phát hành refresh token mới và vô hiệu token cũ. Nếu token cũ bị dùng lại, coi như bị đánh cắp và thu hồi cả chuỗi. Phần lưu refresh token phía server xem ở [auth-opaque-token-sliding-session.md](./auth-opaque-token-sliding-session.md).

## 6. Ghi chú FE

> **FE:** Lưu token ở đâu?
>
> | Nơi lưu | XSS đọc được? | CSRF? |
> |---|---|---|
> | `localStorage` | **Có** | Không |
> | Cookie `HttpOnly; Secure; SameSite` | Không | Cần chống (SameSite, CSRF token) |
>
> Khuyến nghị: refresh token trong cookie `HttpOnly`, access token giữ trong bộ nhớ (biến JS). Khi nhận `401`, FE gọi `/refresh` rồi gửi lại request. Chỉ cho **một** lần refresh chạy cùng lúc, nếu không sẽ gặp [race condition](./race-condition.md).

## 7. Lỗi hay gặp

- Không đặt `exp`, nên token sống vĩnh viễn.
- Chấp nhận `alg: none` hoặc không cố định thuật toán khi verify.
- Dùng secret yếu cho HS256.
- Nhét quyền (roles) vào token rồi tin tuyệt đối. Quyền đổi nhưng token cũ vẫn mang quyền cũ.
- Nhầm "đã mã hóa" với "đã ký": JWT thường chỉ được ký.

## 8. Liên quan

- [Opaque token và sliding session](./auth-opaque-token-sliding-session.md): hướng ngược lại, server nhớ trạng thái.
- [Rate limit](./rate-limit.md): chặn dò mật khẩu ở endpoint `/login`.
- [Cache](./cache.md): lưu `token_version` để tránh tra DB mỗi request.
