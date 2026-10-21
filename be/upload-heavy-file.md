# Upload File Lớn

## 1. Định nghĩa kỹ thuật

**Upload file lớn** là việc truyền các tệp có dung lượng lớn (hàng trăm MB đến GB) từ client lên server/object storage một cách **đáng tin cậy** (chịu được đứt mạng), **không làm cạn tài nguyên** (RAM, băng thông, kết nối) và có thể **tiếp tục từ chỗ dở** (resumable). Các kỹ thuật chính: **streaming**, **presigned URL**, **chunked / multipart upload**.

## 2. Giải thích đơn giản

Chuyển một tủ lạnh vào nhà. Nếu bê cả cái qua cửa nhỏ thì kẹt, mệt, mà rơi là mất hết. Cách khác: **tháo thành từng phần, mang lên từng phần, lắp lại**. Hỏng phần nào chỉ mang lại phần đó.

```
Cách ngây thơ:   Client ═══ 2GB ═══► API server (đọc vào RAM) ═══► Storage
                 hết RAM, timeout, đứt mạng là làm lại từ đầu

Cách tốt:        Client ──xin URL──► API server ──presigned URL──► Client
                 Client ═ chia 8MB/chunk, gửi song song ═► Object storage (S3)
                 API server chỉ nhận thông báo "xong"
```

## 3. Các vấn đề khi upload lớn

| Vấn đề | Hậu quả |
|---|---|
| Đọc cả file vào RAM | Server hết bộ nhớ khi nhiều người upload |
| Request quá dài | Timeout của proxy/LB/CDN, giới hạn kích thước body |
| Mạng chập chờn | Đứt ở 99% thì phải làm lại |
| File đi qua API server | Tốn băng thông và CPU của server nghiệp vụ |
| Không kiểm tra | File độc hại, sai loại, sai dung lượng |

## 4. Các giải pháp

### 4.1 Streaming (đừng buffer)

Server đọc từng khối của request và ghi thẳng ra đích, bộ nhớ chỉ giữ một khối nhỏ.

```ts
req.pipe(fs.createWriteStream(path));          // hoặc pipe thẳng lên storage
// với multipart: dùng busboy/multer streaming, không dùng memoryStorage cho file lớn
```

### 4.2 Presigned URL (đẩy thẳng lên storage)

API chỉ **ký** một URL tạm thời có hạn (ví dụ 10 phút) cho một đường dẫn cụ thể. Client upload **thẳng** lên S3/GCS, không đi qua server.

```
1. FE ─ POST /uploads {name,size,type} ─► BE   (BE kiểm tra quyền, giới hạn size/type)
2. FE ◄─ presigned URL ─────────────────── BE
3. FE ─ PUT file ─────────────────────────► S3
4. FE ─ POST /uploads/:id/complete ───────► BE   (BE ghi metadata vào DB, đưa vào queue xử lý)
```

### 4.3 Chunked / Multipart upload (có thể tiếp tục)

Chia file thành các phần (5–100MB), upload từng phần (có thể song song), cuối cùng ghép lại.

```
file 1GB ─► [c1][c2][c3]...[c128]
              │   │   │
              ▼   ▼   ▼   gửi song song 3–6 chunk
            retry riêng từng chunk lỗi
            ─► "complete" ─► storage ghép thành 1 file
```

| Giao thức/dịch vụ | Ghi chú |
|---|---|
| S3 Multipart Upload | Mỗi part ≥ 5MB (trừ part cuối), trả `ETag` từng part |
| **tus** (tus.io) | Giao thức mở để upload có thể tiếp tục qua HTTP |
| Resumable upload của GCS | Tương tự |

**Tiếp tục khi đứt:** hỏi server "đã nhận những part nào?", chỉ gửi phần còn thiếu.

### 4.4 Kiểm tra toàn vẹn

Tính checksum (MD5/SHA-256) từng chunk và cả file để chắc chắn dữ liệu không hỏng.

## 5. Bảo mật và kiểm soát

- Giới hạn kích thước và loại file ở bước xin URL **và** kiểm tra lại ở bước hoàn tất. Đừng chỉ tin phần mở rộng, hãy kiểm tra magic bytes/MIME thật.
- Lưu file ở storage tách biệt, đặt tên ngẫu nhiên, **không** để người dùng quyết định đường dẫn (path traversal).
- Quét virus bất đồng bộ trước khi cho tải xuống.
- Áp [rate limit](./rate-limit.md) và hạn mức dung lượng theo user.
- Xử lý sau upload (nén ảnh, transcode video) đưa vào [background worker](./background-worker.md) qua [queue](./queue-event.md).

## 6. Ghi chú FE

> **FE:** Chia chunk bằng `File.slice()`, gửi bằng `fetch`/`XMLHttpRequest` (cần `xhr.upload.onprogress` nếu muốn thanh tiến trình), điều khiển số chunk song song để không nghẽn kết nối.
>
> ```ts
> const CHUNK = 8 * 1024 * 1024;
> for (let i = 0; i * CHUNK < file.size; i++) {
>   const blob = file.slice(i * CHUNK, (i + 1) * CHUNK);
>   await putPart(url[i], blob);            // thêm retry + giới hạn song song
> }
> ```
>
> Tính hash nặng nên chạy trong Web Worker (luồng nền của trình duyệt) để không đơ giao diện. Mỗi lần upload cần một khóa riêng, có thể tái dùng ý tưởng [Idempotency Key](./idempotency-key.md) để tránh tạo bản ghi trùng khi bấm lại.

> **DB:** **Không** lưu nội dung file trong DB. Chỉ lưu metadata (tên, size, hash, trạng thái `UPLOADING/READY/FAILED`, đường dẫn storage). Dọn upload dở dang bằng [batch job](./batch-job.md) định kỳ.

## 7. Lỗi hay gặp

- Giới hạn body mặc định của nginx/framework (`client_max_body_size`) chặn file lớn.
- Không dọn các part mở dở trên S3 nên tốn phí lưu trữ (cần lifecycle rule).
- Chỉ validate ở FE.
- Cấp presigned URL quá rộng (không giới hạn path, size, thời hạn).

## 8. Liên quan

- [Edge và CDN](./edge-cdn.md): tăng tốc upload về điểm gần người dùng, phân phối file đã upload.
- [SSE](./sse-streaming.md): báo tiến trình xử lý sau upload.
