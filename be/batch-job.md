# Batch Job

## 1. Định nghĩa kỹ thuật

**Batch job** (batch processing) là một tác vụ xử lý **một khối lượng lớn dữ liệu theo từng lô**, thường **không cần tương tác người dùng**, chạy theo **lịch** (cron), theo sự kiện hoặc thủ công. Khác với xử lý online (từng request, phản hồi tức thì), batch ưu tiên **thông lượng** hơn độ trễ.

## 2. Giải thích đơn giản

Giống **chốt sổ cuối ngày** ở cửa hàng: ban ngày bán từng đơn lẻ, tối đóng cửa rồi tính doanh thu, đối soát, gửi báo cáo một lần cho cả ngày. Ví dụ: tính lương cuối tháng, gửi newsletter, dọn dữ liệu cũ, đối soát thanh toán, đồng bộ dữ liệu.

```
 00:00 ┌──────────── cron kích hoạt ────────────┐
       │ đọc 1000 bản ghi → xử lý → ghi → lặp   │  ← chia thành lô (chunk)
       │ lưu checkpoint sau mỗi lô              │
       └─────────────────────────────────────────┘ 02:10 xong, gửi báo cáo
```

## 3. Cách thiết kế đúng

### 3.1 Chia lô (chunking)

Không đọc hết 10 triệu dòng vào RAM, và không dùng một transaction khổng lồ. Chia lô vài trăm đến vài nghìn dòng:

```sql
-- quét bằng keyset, không dùng OFFSET lớn
SELECT * FROM invoices
WHERE id > :last_id AND status = 'PENDING'
ORDER BY id
LIMIT 1000;
```

`OFFSET` càng về sau càng chậm, hãy dùng [cursor pagination](../db/cursor-pagination.md) (keyset) để quét bảng lớn.

### 3.2 Checkpoint và khả năng chạy lại

Lưu `last_id` đã xử lý. Nếu job chết giữa chừng, chạy lại sẽ **tiếp tục từ checkpoint** chứ không làm lại từ đầu.

### 3.3 Idempotent

Chạy lại hay chạy trùng không được gây hậu quả kép (gửi 2 email, trừ tiền 2 lần). Cách làm: đánh dấu bản ghi đã xử lý, dùng unique constraint, hoặc khóa khử trùng lặp. Xem [Idempotency Key](./idempotency-key.md).

### 3.4 Chống chạy chồng

Job đêm chạy lâu quá 24 giờ, cron kích hoạt thêm bản thứ hai. Cần **khóa** để chỉ một instance chạy:

- Advisory lock của DB: `SELECT pg_try_advisory_lock(12345)`.
- Khóa phân tán trong Redis có TTL.
- Cột trạng thái `RUNNING` cập nhật bằng điều kiện ([optimistic lock](../db/optimistic-lock.md)).

### 3.5 Kích thước transaction

Mỗi lô một [transaction](../db/acid-transaction.md) nhỏ. Transaction dài giữ lock lâu, làm tăng nguy cơ [deadlock](../db/deadlock.md) và chặn các request online.

## 4. Batch job và các khái niệm gần

| | Batch job | [Background worker](./background-worker.md) |
|---|---|---|
| Kích hoạt | Lịch hoặc thủ công | Từng job được đẩy vào queue |
| Khối lượng mỗi lần | Lớn (cả tập dữ liệu) | Nhỏ (một việc) |
| Mục tiêu | Thông lượng | Tách việc chậm khỏi request |

Thực tế thường kết hợp: cron kích hoạt batch, batch chia lô rồi **đẩy từng lô vào queue** cho nhiều worker xử lý song song ([Queue và Event](./queue-event.md)).

## 5. Tác động đến DB

> **DB:** Batch chạy ban đêm vẫn tranh tài nguyên với truy vấn online.
> - Đặt giờ thấp điểm, giới hạn tốc độ (sleep giữa các lô).
> - Cập nhật hàng loạt dùng `INSERT … VALUES (…),(…)` hoặc `COPY`/bulk API thay vì từng dòng.
> - Với bảng khổng lồ, xóa dữ liệu cũ bằng cách bỏ cả partition thay vì `DELETE` hàng triệu dòng: xem [Partitioning](../db/partitioning.md).
> - Cột lọc điều kiện (`status`, `created_at`) cần có [index](../db/index-btree.md).

> **FE:** Batch chạy nền nên FE thường chỉ xem kết quả (trạng thái, báo cáo). Nếu user kích hoạt thủ công ("Xuất báo cáo"), trả `202`, hiện tiến trình qua [SSE](./sse-streaming.md) hoặc polling.

## 6. Lỗi hay gặp

- Load toàn bộ dữ liệu vào bộ nhớ, dẫn tới OOM.
- Một lỗi ở một bản ghi làm sập cả job. Nên bắt lỗi từng bản ghi, ghi vào bảng lỗi để xử lý sau.
- Không giám sát: job lặng lẽ không chạy hoặc chạy quá lâu mà không ai biết. Hãy gửi cảnh báo khi quá hạn.
- Múi giờ và giờ mùa hè làm cron chạy lệch hoặc chạy hai lần.

## 7. Liên quan

- [Background worker](./background-worker.md), [Queue và Event](./queue-event.md)
- [Cursor Pagination](../db/cursor-pagination.md), [Partitioning](../db/partitioning.md)
