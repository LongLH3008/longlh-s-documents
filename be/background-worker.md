# Background Worker

## 1. Định nghĩa kỹ thuật

**Background worker** là một **tiến trình (hoặc luồng) chạy nền, tách biệt với tiến trình xử lý request**. Nó lấy công việc (**job**) từ một hàng đợi và thực thi bất đồng bộ, không nằm trong chu kỳ request/response. Mô hình chung là **producer → queue → consumer (worker)**.

## 2. Giải thích đơn giản

Nhà hàng có **nhân viên phục vụ** (web server) và **đầu bếp** (worker). Phục vụ nhận order, ghi vào phiếu rồi trả lời khách "đã nhận" ngay, sau đó đầu bếp lo nấu. Nếu phục vụ tự nấu, khách sau phải đứng chờ rất lâu.

```
 Client ─► Web server ─► [ Queue ] ─► Worker 1
              │              ▲   └──► Worker 2   ← tăng số worker để tăng thông lượng
              │ trả 202      │
              ▼  Accepted    └── job: { type:"send-email", to:"a@b.c" }
```

Việc nào nên đưa vào worker: gửi email/SMS, resize ảnh, xuất báo cáo, gọi API bên thứ ba chậm, xử lý sau upload, đồng bộ dữ liệu.

## 3. Vòng đời job

```
 pending ─► active ─► completed
              │
              └─ lỗi ─► retry (backoff) ─► ... ─► failed ─► Dead Letter Queue (DLQ)
```

| Khái niệm | Ý nghĩa |
|---|---|
| **Retry + backoff** | Job lỗi thì thử lại sau 1s, 4s, 16s… tránh dồn dập khi dịch vụ ngoài đang chết |
| **DLQ** | Chỗ chứa job thất bại hẳn để người vận hành xem xét |
| **Visibility timeout / lease** | Job đang xử lý bị "khóa" tạm, hết thời gian mà chưa ack thì giao lại cho worker khác |
| **Concurrency** | Số job một worker chạy song song |
| **Graceful shutdown** | Khi deploy, ngừng nhận job mới và đợi job đang chạy xong |

## 4. Nguyên tắc thiết kế

1. **Job phải idempotent.** Hệ thống queue phổ biến giao ít nhất một lần (at-least-once), nên job có thể chạy hai lần. Xem [Idempotency Key](./idempotency-key.md).
2. **Job nhỏ và rõ ràng.** Chỉ truyền ID trong payload, đừng nhét cả dữ liệu lớn. Worker tự đọc bản mới nhất từ DB.
3. **Có timeout.** Job treo sẽ chặn cả slot.
4. **Tránh xử lý trùng giữa nhiều worker**: dùng [pessimistic lock](../db/pessimistic-lock.md) với `SKIP LOCKED`, hoặc để queue đảm bảo mỗi job chỉ giao cho một worker.
5. **Theo dõi:** độ dài hàng đợi, thời gian chờ, tỷ lệ lỗi, tuổi job cũ nhất.

## 5. Ví dụ

```ts
// producer (trong API handler)
await queue.add('send-email', { userId: 42 }, { attempts: 5, backoff: { type: 'exponential', delay: 1000 } });
return res.status(202).json({ status: 'queued' });

// worker (tiến trình riêng)
new Worker('send-email', async (job) => {
  const user = await db.users.find(job.data.userId);
  await mailer.send(user.email, 'Chào mừng');         // phải an toàn khi chạy lại
}, { concurrency: 10 });
```

## 6. So sánh nhanh

| | Chạy trong request | Background worker | [Batch job](./batch-job.md) |
|---|---|---|---|
| Kích hoạt | Request của user | Sự kiện/job được đẩy vào queue | Lịch (cron) hoặc thủ công |
| Chạy khi nào | Ngay lập tức | Gần như ngay | Theo lịch, lượng lớn |
| Hướng tới | Phản hồi nhanh | Tách việc chậm khỏi request | Xử lý hàng loạt |

## 7. Ghi chú FE

> **FE:** Vì xử lý xong sau, FE nhận `202 Accepted` kèm `jobId`, rồi theo dõi kết quả bằng polling hoặc [SSE](./sse-streaming.md). Đừng nhầm với Web Worker: đó là luồng nền **trong trình duyệt**, còn bài này là tiến trình nền **trên server**.

## 8. Liên quan

- [Queue và Event](./queue-event.md): hạ tầng worker lấy job từ đó.
- [Batch job](./batch-job.md): xử lý hàng loạt theo lịch.
- [Upload file lớn](./upload-heavy-file.md): xử lý ảnh/video sau khi upload.
