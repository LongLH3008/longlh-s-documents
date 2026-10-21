# Optimistic Lock

## 1. Định nghĩa kỹ thuật

**Optimistic locking** (khóa lạc quan), hay **optimistic concurrency control (OCC)**, là chiến lược trong đó transaction **không khóa** khi đọc, với giả định xung đột *hiếm khi xảy ra*. Đến lúc **ghi**, hệ thống kiểm tra xem dữ liệu có bị ai sửa kể từ lần đọc không (thường bằng **cột version** hoặc timestamp). Nếu có, thao tác ghi bị từ chối và ứng dụng phải xử lý (đọc lại, thử lại hoặc báo người dùng).

## 2. Giải thích đơn giản

Hai người cùng mở một tài liệu chung để sửa. Không ai bị chặn. Khi bấm **Lưu**, hệ thống hỏi: "Từ lúc bạn mở, có ai lưu bản mới hơn chưa?" Nếu có, từ chối và nói "Bản của bạn đã cũ, hãy tải lại". Không ai phải chờ, chỉ người đến sau bị báo xung đột.

```
T1: đọc (v=3) ────────── sửa ─────────► UPDATE ... WHERE v=3  ✔ thành công, v→4
T2: đọc (v=3) ──── sửa ───────────────────────► UPDATE ... WHERE v=3  ✘ 0 dòng (v đã là 4)
                                                  → xung đột: đọc lại / retry / báo user
```

## 3. Cách làm với cột version

```sql
ALTER TABLE products ADD COLUMN version INT NOT NULL DEFAULT 0;

-- 1) đọc
SELECT id, stock, version FROM products WHERE id = 7;   -- version = 3

-- 2) ghi có điều kiện
UPDATE products
SET stock = 9, version = version + 1
WHERE id = 7 AND version = 3;
-- số dòng bị ảnh hưởng = 1 → thành công
-- số dòng bị ảnh hưởng = 0 → có người sửa trước → xung đột
```

```ts
// BE
const { count } = await db.product.updateMany({
  where: { id, version: current.version },
  data: { stock: newStock, version: { increment: 1 } },
});
if (count === 0) throw new ConflictError();     // → HTTP 409
```

ORM thường hỗ trợ sẵn: JPA/Hibernate `@Version`, Entity Framework `rowversion`, Rails `lock_version`.

## 4. Xử lý khi xung đột

| Cách | Khi nào |
|---|---|
| **Tự retry** (đọc lại, tính lại, ghi lại) | Phép tính lại được và không cần hỏi người dùng (trừ tồn kho) |
| **Trả `409 Conflict`** | Cần người dùng quyết định (hai người sửa cùng một bài viết) |
| **Merge** | Trường khác nhau thì gộp tự động, cùng trường thì hỏi |

Vòng retry phải có giới hạn số lần và độ trễ ngẫu nhiên để tránh dồn dập khi tải cao.

## 5. Ưu và nhược điểm

| Ưu | Nhược |
|---|---|
| Không giữ khóa nên không chặn ai, không [deadlock](./deadlock.md) | Phải viết xử lý xung đột / retry |
| Thông lượng cao khi xung đột hiếm | Khi xung đột nhiều, retry liên tục gây lãng phí |
| Hợp với thao tác "dài" (người dùng mở form một lúc) | Người đến sau mất công sức làm |
| Không phụ thuộc connection đang mở | Phải thêm cột và nhớ điều kiện `WHERE version = ?` |

## 6. So sánh với Pessimistic lock

| | **Optimistic** | [**Pessimistic**](./pessimistic-lock.md) |
|---|---|---|
| Giả định | Xung đột hiếm | Xung đột thường xuyên |
| Cách làm | Kiểm tra lúc ghi | Khóa lúc đọc |
| Người khác | Không bị chặn | Phải chờ |
| Xung đột xử lý | Thất bại rồi retry | Tránh ngay từ đầu |
| Rủi ro chính | Retry bão | Chờ lâu, deadlock |
| Hợp với | Sửa hồ sơ, bài viết, cấu hình | Tồn kho "hot", số dư |

Nếu thao tác chỉ là "cộng/trừ một số", dùng `UPDATE … SET x = x - 1 WHERE x >= 1` (atomic) là đủ, không cần version. Xem [Race condition](../be/race-condition.md).

## 7. Biến thể qua HTTP: ETag và If-Match

Cùng một ý tưởng nhưng ở tầng API (không cần lộ cột `version`):

```
GET  /articles/5        ─►  200  ETag: "v3"
PUT  /articles/5
     If-Match: "v3"     ─►  200 (khớp)   hoặc   412 Precondition Failed (đã bị sửa)
```

## 8. Ghi chú FE

> **FE:** Đây là nơi optimistic lock có giá trị lớn nhất. Form lưu dữ liệu giữ lại `version`/`ETag` nhận lúc tải và gửi kèm khi lưu. Khi nhận `409/412`, hiển thị "Dữ liệu đã được người khác cập nhật" kèm lựa chọn **Tải lại** hoặc **Ghi đè/Hợp nhất**, và đừng làm mất nội dung người dùng đã nhập.

> **BE:** Cột version phải được cập nhật ở **mọi** nơi ghi vào bảng, nếu có một đường ghi bỏ qua nó thì cơ chế vô tác dụng.

## 9. Liên quan

- [Pessimistic lock](./pessimistic-lock.md), [Isolation levels](./isolation-levels.md)
- [Race condition](../be/race-condition.md), [Idempotency Key](../be/idempotency-key.md)
