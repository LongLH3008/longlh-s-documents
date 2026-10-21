# Queue và Event

## 1. Định nghĩa kỹ thuật

**Message queue** là thành phần trung gian lưu các **message** theo thứ tự, cho phép **producer** gửi và **consumer** nhận một cách **bất đồng bộ và tách rời** (decoupled).

**Event** là bản ghi về một **sự việc đã xảy ra** ("OrderPlaced"), bất biến và thường được phát cho nhiều bên quan tâm. **Event-driven architecture** là kiến trúc mà các thành phần giao tiếp chủ yếu qua event.

Phân biệt hai ý hay bị nhầm:

| | **Command / Job** | **Event** |
|---|---|---|
| Ý nghĩa | "Hãy làm X" | "X đã xảy ra" |
| Người nhận | Đúng một bên xử lý | Không hoặc nhiều bên |
| Mô hình | **Queue** (point-to-point) | **Pub/Sub** (publish-subscribe) |

## 2. Giải thích đơn giản

- **Queue** giống **hộp thư phiếu việc**: ai rảnh thì lấy một phiếu, mỗi phiếu chỉ một người làm.
- **Pub/Sub** giống **loa thông báo**: phát một lần, ai đăng ký nghe thì đều nghe được.

```
Queue (mỗi message → 1 consumer)         Pub/Sub (mỗi event → mọi subscriber)
  P ─► [m3 m2 m1] ─► C1                     P ─► Topic ─┬─► Email service
                  └► C2  (chia nhau)                    ├─► Inventory service
                                                        └─► Analytics service
```

Lợi ích: chịu tải đột biến (queue đệm lại), service chết không làm mất việc, thêm tính năng mới chỉ cần thêm subscriber.

## 3. Đảm bảo giao message

| Mức | Nghĩa | Hệ quả |
|---|---|---|
| At-most-once | Gửi tối đa 1 lần | Có thể **mất** message |
| **At-least-once** | Gửi ít nhất 1 lần | Có thể **trùng**, phổ biến nhất |
| Exactly-once | Đúng 1 lần | Rất khó trên mạng, thường đạt bằng at-least-once + **idempotent consumer** |

```
consumer: nhận message ─► xử lý ─► ACK
                            │
              chết trước khi ACK → broker giao lại → xử lý lần 2 (trùng)
```

Vì vậy consumer phải **idempotent**: xem [Idempotency Key](./idempotency-key.md) (dùng message ID làm khóa khử trùng lặp).

## 4. Các khái niệm cần biết

- **ACK / NACK:** consumer xác nhận đã xử lý xong / thất bại.
- **Dead Letter Queue (DLQ):** nơi chứa message lỗi nhiều lần.
- **Ordering:** thứ tự thường chỉ được đảm bảo **trong cùng một partition/key** (Kafka), không phải toàn cục.
- **Backpressure:** consumer chậm hơn producer thì queue phình ra, cần giám sát độ dài hàng đợi.
- **Retention / replay:** Kafka giữ log để đọc lại, còn queue kiểu SQS/RabbitMQ xóa sau khi ACK.

| Công cụ | Đặc trưng |
|---|---|
| RabbitMQ | Broker truyền thống, routing linh hoạt, xóa sau ACK |
| Amazon SQS | Queue được quản lý, đơn giản, at-least-once |
| Kafka | Log phân tán, giữ lại event, replay, thông lượng rất lớn |
| Redis (Streams/BullMQ) | Nhẹ, tiện cho job nền quy mô vừa |

## 5. Outbox pattern: ghi DB và phát event phải đi cùng nhau

Vấn đề: ghi DB thành công nhưng gửi event thất bại (hoặc ngược lại) khiến hệ thống lệch trạng thái, vì DB và broker không chung [transaction](../db/acid-transaction.md).

```
 BEGIN
   INSERT INTO orders ...
   INSERT INTO outbox (event) ...   ← cùng transaction, cùng thành công hoặc cùng rollback
 COMMIT
        │
        ▼ tiến trình relay đọc bảng outbox và publish lên broker, rồi đánh dấu đã gửi
```

## 6. Ghi chú FE và DB

> **FE:** Xử lý qua queue là bất đồng bộ, nên UI phải thiết kế cho trạng thái "đang xử lý": trả `202`, hiện trạng thái pending, cập nhật bằng [SSE](./sse-streaming.md) hoặc polling.

> **DB:** Bảng `outbox` cần index trên cột trạng thái/thời gian ([index](../db/index-btree.md)). Nếu dùng bảng làm queue, hãy lấy job bằng `SELECT … FOR UPDATE SKIP LOCKED` ([pessimistic lock](../db/pessimistic-lock.md)).

## 7. Lỗi hay gặp

- Coi queue là "đảm bảo đúng 1 lần" rồi không làm consumer idempotent.
- Không có DLQ, nên message lỗi bị retry vô hạn và chặn hàng đợi.
- Message quá lớn. Chỉ nên chứa ID và tham chiếu.
- Phụ thuộc thứ tự toàn cục trong hệ thống nhiều consumer.

## 8. Liên quan

- [Background worker](./background-worker.md): consumer xử lý job.
- [Batch job](./batch-job.md)
- [Idempotency Key](./idempotency-key.md)
