# Idempotency Key

## 1. Idempotency là gì

Một thao tác là **idempotent** khi gọi 1 lần hay N lần đều cho ra **cùng một trạng thái cuối** trên server. Đây là thuộc tính của *kết quả*, không phải của *hành vi bên trong*.

| Method | Idempotent theo spec HTTP? | Vì sao |
|---|---|---|
| GET | Có | Chỉ đọc, không đổi trạng thái |
| PUT | Có | "Đặt" tài nguyên = giá trị tuyệt đối, gọi lại vẫn ra giá trị đó |
| DELETE | Có | Sau lần đầu, tài nguyên đã không tồn tại; gọi lại vẫn vậy |
| PATCH | Tuỳ cách viết | `{ set: x }` thì có; `{ increment: x }` thì không |
| POST | Không | Ngữ nghĩa là "tạo thêm" → mỗi lần gọi là một sự kiện mới |

**Lưu ý:** idempotent theo spec không tự động đúng trong code. Nếu PUT được implement kiểu `balance: { increment: amount }` thì dù dùng đúng method PUT, nó vẫn không idempotent thật sự. HTTP method chỉ là *hợp đồng ngữ nghĩa*, không tự bảo vệ khỏi lỗi logic.

## 2. Vấn đề cần giải quyết

Mạng không đáng tin. Với POST (tạo đơn, thanh toán, rút tiền...):

```
1. FE gửi POST /orders
2. BE xử lý xong, tạo đơn
3. Response bị rớt trên đường về (timeout, mất mạng, user F5)
4. FE không biết BE đã xử lý hay chưa → gửi lại
5. BE tạo thêm 1 đơn nữa → trùng lặp, có thể là trừ tiền 2 lần
```

**Idempotency key** là chuỗi ID duy nhất đại diện cho **một ý định hành động** của user, gửi kèm request (thường qua header `Idempotency-Key`). BE dùng key này để nhận diện "request này đã xử lý rồi" và trả lại đúng kết quả cũ thay vì xử lý lại.

```
Lần 1: POST /orders + Idempotency-Key: abc-123 → xử lý → lưu kết quả theo key → trả về
Lần 2 (retry, cùng key): POST /orders + Idempotency-Key: abc-123 → thấy key đã có → trả lại kết quả cũ, KHÔNG xử lý lại
```

## 3. Đây là kỹ thuật gì

Idempotency key là một **pattern ở tầng ứng dụng**, thuộc nhóm *reliability patterns* xử lý bài toán **at-least-once delivery** trong hệ thống phân tán — tức khi hệ thống chấp nhận request/message có thể bị gửi lại, và cần cơ chế để việc gửi lại không gây hậu quả cộng dồn.

Nó **không phải là system design** — không mô tả kiến trúc tổng thể, mà là một building block cụ thể áp dụng vào một điểm (API POST có side-effect nguy hiểm khi trùng), tương tự như quyết định dùng JWT hay REST khi thiết kế hệ thống.

Các kỹ thuật họ hàng, cùng hướng tới *exactly-once semantics*:

| Kỹ thuật | Vấn đề giải quyết |
|---|---|
| Idempotency key | Chặn POST bị gọi trùng tạo side-effect trùng |
| Deduplication ID (Kafka, SQS) | Chặn message queue xử lý trùng 1 message |
| Optimistic locking / version field | Chặn 2 update ghi đè nhau do race condition |
| Outbox pattern | Đảm bảo ghi DB + publish event là atomic |

## 4. Khi nào cần dùng

**Cần:**
- POST tạo tài nguyên có hậu quả thật khi trùng: đặt hàng, thanh toán, chuyển/rút tiền, gửi mail quan trọng.

**Không cần:**
- GET (tự idempotent sẵn).
- PUT/DELETE implement đúng chuẩn (tự idempotent sẵn).
- POST không nguy hiểm khi trùng: log event, tăng lượt xem, thao tác cho phép lặp lại (thêm sản phẩm vào giỏ).

Áp dụng có chọn lọc — không cần bọc toàn bộ POST trong hệ thống.

## 5. Nguyên tắc thiết kế key

**Key đại diện cho "ý định nghiệp vụ", không đại diện cho "một lần bấm nút" hay "một request".**

### Ưu tiên deterministic key (sinh từ entity nghiệp vụ)

```
checkout:{cartId}
withdraw:{userId}:{walletId}
```

Ưu điểm: 2 tab cùng thao tác 1 đối tượng (ví dụ cùng giỏ hàng) sẽ tự nhiên hội tụ về cùng 1 key mà không cần đồng bộ gì thêm giữa các tab. `sessionStorage` scope theo từng tab riêng biệt, nên nếu dùng random UUID thuần, 2 tab sẽ sinh 2 key khác nhau → có thể tạo trùng khi user vô tình thao tác song song.

### Dùng random UUID khi không có entity cố định để bám vào

Ví dụ: form feedback không gắn với ID nào. Sinh 1 lần khi mở form, giữ trong state/sessionStorage.

```javascript
const key = crypto.randomUUID(); // sinh 1 lần khi bắt đầu hành động, KHÔNG sinh lại mỗi lần retry
```

### Vòng đời key ở FE

```
Sinh key (bắt đầu hành động)
   ↓
Giữ nguyên qua mọi lần gọi lại (kể cả retry tự động)
   ↓
Xoá key CHỈ KHI:
   - Request thành công, HOẶC
   - Lỗi business/validation rõ ràng (400, 422 — BE chắc chắn chưa xử lý)
   ↓
GIỮ NGUYÊN key khi:
   - Network error, timeout
   - 5xx không rõ nguyên nhân
   - 409 (đang bị lock do request khác cùng key đang chạy)
```

Nguyên tắc khi phân vân: **giữ nhầm** (user tạm thời không bấm lại được cho tới khi TTL hết) hậu quả nhẹ hơn **xoá nhầm** (gây tạo trùng thật). Nên mặc định nghiêng về giữ.

## 6. Việc cần làm khi triển khai

### Phía Backend

- [ ] Chọn store có TTL + lock (Redis khuyến nghị: `SET key val EX ttl NX` cho lock).
- [ ] Thiết kế scope key: `{userId}:{method}:{path}:{keyClientGửi}` — tránh user A trùng key với user B, hoặc key bị dùng nhầm route khác.
- [ ] Interceptor/Middleware:
  - Validate header `Idempotency-Key` có mặt (bắt buộc với route đã áp dụng).
  - `get(key)` → nếu đã có kết quả, trả lại luôn, không xử lý lại.
  - `lock(key)` → nếu đang có request khác cùng key chạy, trả **409 Conflict**.
  - Xử lý xong → `save(key, result, ttl)`.
  - Lỗi → `unlock(key)` để cho phép retry hợp lệ.
- [ ] Set TTL theo nghiệp vụ: vài phút (retry tự động do mạng) tới 24h (thao tác tài chính, user có thể rời trang quay lại sau).
- [ ] Áp interceptor có chọn lọc lên từng route, không áp toàn cục.
- [ ] (Nâng cao) Hash kèm payload cùng key, để phát hiện "cùng key nhưng khác nội dung" → trả lỗi rõ ràng thay vì âm thầm trả kết quả cũ.

### Phía Frontend

- [ ] Helper sinh/đọc/xoá key theo `scopeKey` (deterministic theo entity nếu có).
- [ ] Xác định `scopeKey` cho từng luồng nghiệp vụ cần bảo vệ.
- [ ] Wrapper gọi API: gắn header `Idempotency-Key`, xử lý giữ/xoá key theo loại lỗi trả về (mục 5).
- [ ] Disable nút submit trong lúc đang chờ response — lớp phòng thủ rẻ, chặn phần lớn double-submit trước khi cần tới key.
- [ ] Nếu có tầng BFF/Route Handler trung gian: **forward** header nguyên vẹn, không tự sinh key ở tầng proxy (key phải sống từ browser để chịu được F5/nhiều request).
- [ ] Nếu cần chịu được F5 giữa chừng: lưu key vào `sessionStorage` (theo `scopeKey`), xoá khi hành động kết thúc.

### Hạ tầng / vận hành

- [ ] Đảm bảo Redis (hoặc store tương đương) có sẵn, theo dõi memory vì key + kết quả cache theo TTL.
- [ ] Log lại các lần key bị trùng (cache hit) để theo dõi tần suất retry thực tế — dữ liệu này giúp tinh chỉnh TTL.
- [ ] Viết test: gọi cùng 1 request 2 lần liên tiếp với cùng key → xác nhận chỉ có 1 bản ghi được tạo trong DB.

## 7. Bẫy thường gặp

- Sinh key **bên trong** hàm gọi API (kể cả trong logic retry) → mỗi lần retry ra key mới → mất tác dụng.
- Xoá key ngay sau khi gửi request (không đợi biết kết quả) → nếu response bị rớt, retry sẽ dùng key mới → tạo trùng.
- Dùng chung 1 key cho nhiều hành động khác nhau của cùng 1 user → BE nhầm là cùng 1 request, trả sai kết quả.
- Không scope key theo user → user khác có thể vô tình (hoặc cố ý) tái sử dụng key đã xử lý.
- Coi đây là giải pháp thay thế cho database transaction/constraint — idempotency key chống trùng do retry, không thay thế được unique constraint hay transaction khi cần đảm bảo tính toàn vẹn dữ liệu ở tầng DB.