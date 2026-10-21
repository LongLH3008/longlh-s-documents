# Rate Limit

## 1. Định nghĩa kỹ thuật

**Rate limiting** là cơ chế kiểm soát **số lượng request** một client (định danh theo IP, user, API key…) được phép gửi tới một tài nguyên trong một **khoảng thời gian** nhất định. Request vượt ngưỡng bị từ chối hoặc làm chậm. Mục tiêu là bảo vệ tài nguyên, đảm bảo công bằng và chống lạm dụng.

## 2. Giải thích đơn giản

Giống **cổng soát vé có hạn mức**: mỗi người chỉ được vào 10 lần mỗi phút. Quá số đó thì bảo vệ nói "đợi chút rồi quay lại". Nhờ vậy một người (hoặc một con bot) không thể chiếm hết chỗ của mọi người khác.

Dùng để:

- Chống brute-force mật khẩu, spam OTP.
- Chống scraping và DDoS tầng ứng dụng.
- Bảo vệ DB/dịch vụ phía sau khỏi quá tải.
- Chia gói dịch vụ (free 100 req/ngày, pro 10.000 req/ngày).

## 3. Các thuật toán

### 3.1 Fixed window

Đếm trong từng cửa sổ cố định (ví dụ mỗi phút).

```
Giới hạn 5 req/phút
  00:00 ───────── 01:00 ───────── 02:00
        ░░░░░ 5 ok        ░░░░░ 5 ok
                    ▲ 00:59 gửi 5, 01:00 gửi 5 → 10 request trong 2 giây
```

Đơn giản nhưng có **lỗi biên**: có thể bị gấp đôi hạn mức quanh ranh giới cửa sổ.

### 3.2 Sliding window (log / counter)

Cửa sổ trượt: đếm số request trong "N giây gần nhất" tính từ hiện tại. Bản **log** lưu timestamp từng request (chính xác nhưng tốn bộ nhớ), bản **counter** nội suy từ 2 cửa sổ liền kề (xấp xỉ, nhẹ).

### 3.3 Token bucket (phổ biến nhất)

```
        nạp r token/giây
              │
         ┌────▼────┐   dung lượng tối đa = B (burst)
         │ ● ● ● ● │
         └────┬────┘
              │ mỗi request lấy 1 token
              ▼
   có token → cho qua      hết token → 429
```

Cho phép **burst** (dồn lượng ngắn hạn, tối đa B) trong khi vẫn giữ tốc độ trung bình r. Hợp với API thực tế.

### 3.4 Leaky bucket

Request vào hàng đợi rồi chảy ra với **tốc độ đều**. Làm phẳng lưu lượng, nhưng không cho burst.

| Thuật toán | Burst | Độ chính xác | Bộ nhớ |
|---|---|---|---|
| Fixed window | Lỗi biên | Thấp | Rất nhẹ |
| Sliding log | Không | Cao | Nặng |
| Sliding counter | Gần đúng | Khá | Nhẹ |
| Token bucket | **Có** | Tốt | Nhẹ |
| Leaky bucket | Không | Tốt | Nhẹ (hàng đợi) |

## 4. Triển khai với Redis (nhiều server)

Bộ đếm phải **dùng chung** giữa các instance, nên đặt ở Redis. Phải thao tác **atomic**, nếu không sẽ dính [race condition](./race-condition.md) (hai request cùng đọc "còn 1 lượt" rồi cùng đi qua).

```
-- fixed window bằng INCR (atomic)
key = "rl:{user}:{minute}"
n = INCR key
if n == 1 then EXPIRE key 60 end
if n > LIMIT then reject end
```

Đoạn này nên đóng gói thành Lua script để `INCR` và `EXPIRE` chạy như một thao tác duy nhất.

## 5. Đặt ở đâu

```
Client ─► [CDN/Edge] ─► [API Gateway / LB] ─► [App middleware] ─► [DB]
            chặn sớm         chặn theo route      chặn theo user      lớp cuối
```

Càng chặn **sớm** càng rẻ. Xem [edge-cdn.md](./edge-cdn.md). Lớp app biết `user_id` nên giới hạn được chính xác theo từng người dùng.

**Chọn khóa:** IP (dễ bị NAT dùng chung hoặc bị giả), user ID (sau đăng nhập), API key, hoặc kết hợp. Endpoint nhạy cảm (`/login`, `/otp`) nên có hạn mức riêng và chặt hơn.

## 6. Phản hồi chuẩn

```
HTTP/1.1 429 Too Many Requests
Retry-After: 30
RateLimit-Limit: 100
RateLimit-Remaining: 0
RateLimit-Reset: 30
```

`429` kèm `Retry-After` giúp client biết khi nào thử lại.

## 7. Ghi chú FE

> **FE:** Khi nhận `429`:
> - Đọc `Retry-After` và chờ đúng thời gian đó.
> - Retry bằng **exponential backoff + jitter** (chờ 1s, 2s, 4s… cộng độ lệch ngẫu nhiên) để các client không đồng loạt retry cùng lúc.
> - Giảm số request từ gốc: [debounce/throttle](../fe/debounce-throttle.md) cho ô tìm kiếm, scroll.
> - Retry request ghi dữ liệu thì phải kèm [Idempotency Key](./idempotency-key.md).

> **DB:** Rate limit cũng là lớp bảo vệ DB khỏi truy vấn nặng. Với các phép quét lớn, kết hợp [cursor pagination](../db/cursor-pagination.md) để mỗi request nhẹ hơn.

## 8. Lỗi hay gặp

- Đếm theo IP sau reverse proxy mà không đọc đúng `X-Forwarded-For`, nên mọi người dùng chung 1 IP của proxy.
- Bộ đếm lưu trong RAM từng instance, nên hạn mức thực tế nhân lên N lần.
- Không có hạn mức riêng cho endpoint nhạy cảm.
- Fail-open/fail-closed không được cân nhắc: khi Redis chết, nên cho qua hay chặn hết?

## 9. Liên quan

- [Edge và CDN](./edge-cdn.md): chặn request từ xa trước khi chạm server.
- [Auth JWT](./auth-jwt-token-version.md): chống dò mật khẩu.
- [Cache](./cache.md): Redis dùng chung cho bộ đếm.
