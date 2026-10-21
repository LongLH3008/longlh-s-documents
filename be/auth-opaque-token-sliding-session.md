# Auth bằng Opaque Token và Sliding Session

## 1. Định nghĩa kỹ thuật

**Opaque token** (reference token) là chuỗi **ngẫu nhiên không mang ý nghĩa** với client. Nó chỉ là một khóa tham chiếu (reference) tới dữ liệu phiên lưu ở server (Redis/DB). Server xác thực bằng cách **tra cứu** token, còn việc kiểm tra "token đang hoạt động" ở dạng chuẩn gọi là **Token Introspection** (RFC 7662).

**Sliding expiration** là chính sách hết hạn **trượt**: mỗi lần user hoạt động, thời điểm hết hạn được gia hạn thêm một khoảng cố định. Ngược lại, **absolute expiration** là hạn cứng tính từ lúc đăng nhập, không gia hạn.

## 2. Giải thích đơn giản

Opaque token giống **thẻ gửi xe có số**. Thẻ không chứa thông tin gì, chỉ có số. Bảo vệ nhìn số rồi tra sổ để biết xe nào, chủ nào, còn hạn không. Muốn hủy thẻ chỉ cần gạch số đó khỏi sổ.

```
 Client                          Server                    Redis
   │ POST /login                   │                         │
   │ ────────────────────────────► │  tạo token ngẫu nhiên   │
   │                               │  SET sess:9f3a… {uid:42}│
   │ ◄── Set-Cookie: sid=9f3a… ─── │ ──────────────────────► │ EX 1800
   │                               │                         │
   │ GET /me (cookie sid)          │                         │
   │ ────────────────────────────► │  GET sess:9f3a… ──────► │
   │                               │ ◄── {uid:42} ────────── │
   │ ◄────────── 200 ───────────── │  EXPIRE sess:9f3a… 1800│  ← gia hạn (sliding)
```

Token tạo bằng bộ sinh số ngẫu nhiên an toàn (CSPRNG), tối thiểu 128 bit entropy. Không dùng `Math.random()` hay ID tăng dần.

## 3. Sliding và Absolute

```
Sliding (idle timeout = 30 phút)
 đăng nhập        hoạt động     hoạt động           im lặng 30'
    │──────30'──────│──────30'──────│──────30'─────────┤ hết hạn
    t0              t1              t2                 t3

Absolute (max = 8 giờ)
    │──────────────────────── 8 giờ ───────────────────┤ hết hạn dù vẫn hoạt động
```

**Chỉ dùng sliding thì phiên có thể sống vô hạn**, nếu token bị đánh cắp kẻ trộm cứ dùng để gia hạn mãi. Thực tế nên kết hợp cả hai:

| Tham số | Ví dụ | Mục đích |
|---|---|---|
| Idle timeout (sliding) | 30 phút | Người dùng bỏ máy thì phiên tự hết |
| Absolute timeout | 8 giờ – 30 ngày | Buộc đăng nhập lại định kỳ |

Gia hạn cho mọi request sẽ ghi store quá nhiều. Cách tối ưu là chỉ gia hạn khi thời gian còn lại dưới một nửa idle timeout.

## 4. So sánh với JWT

| Tiêu chí | Opaque token | JWT ([xem](./auth-jwt-token-version.md)) |
|---|---|---|
| Server lưu trạng thái? | **Có** (stateful) | Không (stateless) |
| Mỗi request tra store? | Có | Không (chỉ verify chữ ký) |
| Thu hồi tức thì | **Dễ**, xóa key là xong | Khó, cần token version hoặc blacklist |
| Đổi quyền có hiệu lực ngay | Có | Chỉ khi token hết hạn |
| Scale nhiều service | Cần store dùng chung | Chỉ cần public key |
| Lộ nội dung cho client | Không | Có (payload đọc được) |

**Chọn thế nào:** web app có đăng nhập, cần "logout ngay" thì opaque token là mặc định tốt. Nhiều microservice gọi nhau, cần verify không phụ thuộc store trung tâm thì JWT ngắn hạn phù hợp hơn. Nhiều hệ thống dùng **cả hai**: refresh token là opaque (thu hồi được), access token là JWT ngắn hạn.

## 5. Refresh token rotation

```
 refresh token R1 ──► /refresh ──► phát hành R2, đánh dấu R1 = đã dùng
 nếu R1 bị gửi lại ──► phát hiện tái sử dụng ──► thu hồi cả "family" R1,R2,… ──► bắt đăng nhập lại
```

Cách này phát hiện được token bị trộm: cả kẻ trộm và chủ thật đều dùng R1, một trong hai sẽ bị lộ ở lần thứ hai.

## 6. Ghi chú FE và DB

> **FE:** Gửi qua cookie `HttpOnly; Secure; SameSite=Lax`. FE không cần (và không thể) đọc token. Khi nhận `401` thì chuyển sang trang đăng nhập. Nhớ gửi `credentials: 'include'` với `fetch` cross-origin.

> **DB:** Bảng/key phiên cần cột `expires_at` và index (xem [index-btree](../db/index-btree.md)) để dọn phiên hết hạn. Với Redis, dùng TTL (`EXPIRE`) để tự xóa. Khi dọn bằng job định kỳ, xem [batch-job](./batch-job.md).

## 7. Lỗi hay gặp

- Không đổi session ID sau khi đăng nhập (session fixation). Hãy luôn phát hành token mới khi quyền thay đổi.
- Cookie thiếu `HttpOnly`/`Secure`.
- Chỉ có sliding, không có absolute timeout.
- Lưu token thô trong DB. Nên lưu **hash** của token, giống mật khẩu.

## 8. Liên quan

- [Auth JWT và Token Version](./auth-jwt-token-version.md)
- [Cache](./cache.md): Redis làm session store.
- [Rate limit](./rate-limit.md): chặn thử token/đăng nhập liên tục.
