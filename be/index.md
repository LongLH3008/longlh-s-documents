# Backend

> Mục lục các kỹ thuật backend.

## Tài liệu

### Xác thực

- [Auth JWT và Token Version](./auth-jwt-token-version.md): JWT stateless, thu hồi token bằng token version, access/refresh token
- [Auth Opaque Token và Sliding Session](./auth-opaque-token-sliding-session.md): token tham chiếu lưu ở server, sliding và absolute expiration

### Bảo vệ và hiệu năng

- [Rate Limit](./rate-limit.md): fixed/sliding window, token bucket, leaky bucket, Redis
- [Edge và CDN](./edge-cdn.md): cache gần người dùng, WAF, chặn request xấu trước khi tới server
- [Cache](./cache.md): cache-aside, TTL, eviction, stampede, penetration, avalanche

### Giao tiếp thời gian thực và nền

- [SSE và Streaming](./sse-streaming.md): server đẩy dữ liệu một chiều, response streaming
- [Background Worker](./background-worker.md): tiến trình nền xử lý job, retry, DLQ
- [Queue và Event](./queue-event.md): message queue, pub/sub, at-least-once, outbox
- [Batch Job](./batch-job.md): xử lý hàng loạt theo lô, checkpoint, chống chạy chồng
- [Upload File Lớn](./upload-heavy-file.md): streaming, presigned URL, chunked/multipart upload

### Độ tin cậy và thiết kế

- [Idempotency Key](./idempotency-key.md): chống xử lý trùng khi retry
- [Race Condition](./race-condition.md): lost update, check-then-act và các cách phòng chống
- [Dependency Injection](./dependency-injection.md): tách phụ thuộc, dễ test
- [Circular Dependencies](./circular-dependencies.md): phụ thuộc vòng, cách phát hiện và gỡ
