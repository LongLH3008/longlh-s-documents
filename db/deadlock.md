# Deadlock

## 1. Định nghĩa kỹ thuật

**Deadlock** (bế tắc) là trạng thái trong đó hai hay nhiều tiến trình/transaction **chờ lẫn nhau theo vòng tròn**: mỗi bên đang giữ một tài nguyên mà bên kia cần, và không bên nào có thể tiếp tục. Deadlock chỉ xảy ra khi đồng thời thỏa **bốn điều kiện Coffman**:

1. **Mutual exclusion:** tài nguyên chỉ một bên dùng tại một thời điểm.
2. **Hold and wait:** vừa giữ một tài nguyên vừa chờ tài nguyên khác.
3. **No preemption:** không thể cưỡng đoạt tài nguyên đang bị giữ.
4. **Circular wait:** tồn tại vòng chờ.

Phá vỡ **một** điều kiện là đủ để ngăn deadlock.

## 2. Giải thích đơn giản

Hai chiếc xe ở hai đầu cây cầu một làn, cùng lái vào giữa. Không ai lùi, không ai đi được. Hoặc: hai người cùng cầm một chiếc đũa, mỗi người chờ người kia buông đũa còn lại.

```
Transaction 1                       Transaction 2
─────────────                       ─────────────
khóa dòng A  ✔                      khóa dòng B  ✔
muốn khóa B  ⏳ ── chờ T2 ───►
             ◄── chờ T1 ─── ⏳ muốn khóa A
                    ↺ vòng chờ → không ai tiến được
```

![Hai tiến trình P1, P2 và hai tài nguyên R1, R2: mỗi tiến trình giữ một tài nguyên và chờ tài nguyên kia](https://upload.wikimedia.org/wikipedia/commons/2/28/Process_deadlock.svg)

*P1 giữ R2 và chờ R1, P2 giữ R1 và chờ R2, tạo thành vòng chờ. Ảnh: [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Process_deadlock.svg).*

```sql
-- T1                                   -- T2
BEGIN;                                  BEGIN;
UPDATE acc SET b=b-10 WHERE id=1;       UPDATE acc SET b=b-10 WHERE id=2;
UPDATE acc SET b=b+10 WHERE id=2;  ⏳    UPDATE acc SET b=b+10 WHERE id=1;  ⏳
```

## 3. DB xử lý thế nào

DB duy trì **đồ thị chờ (wait-for graph)** và định kỳ tìm chu trình. Khi phát hiện, nó chọn một transaction làm **nạn nhân (victim)**, **hủy** transaction đó để phá vòng chờ, các transaction còn lại tiếp tục.

| DB | Lỗi trả về |
|---|---|
| PostgreSQL | `ERROR: deadlock detected`, SQLSTATE `40P01` |
| MySQL InnoDB | `ERROR 1213 (40001): Deadlock found when trying to get lock` |
| SQL Server | Lỗi 1205 |

Nạn nhân chỉ **bị rollback**, không mất dữ liệu. Ứng dụng phải **chạy lại** cả transaction.

## 4. Cách phòng tránh

| Cách | Giải thích |
|---|---|
| **Khóa theo thứ tự cố định** | Luôn khóa `id` nhỏ trước. Cả hai transaction cùng đi một chiều nên không tạo vòng. Hiệu quả nhất |
| **Giữ transaction ngắn** | Giữ khóa càng ngắn, cửa sổ xung đột càng nhỏ |
| **Khóa tất cả cùng lúc** | `SELECT … WHERE id IN (1,2) ORDER BY id FOR UPDATE` |
| **Truy cập theo cùng thứ tự** | Cập nhật các bảng theo trình tự thống nhất trong toàn codebase |
| **Có index tốt** | Thiếu [index](./index-btree.md) khiến DB khóa nhiều dòng hơn cần thiết, tăng xung đột |
| **Cân nhắc [optimistic lock](./optimistic-lock.md)** | Không giữ khóa nên không có hold-and-wait |
| **Đặt timeout** | `lock_timeout` để không chờ vô hạn |
| **Hạ isolation nếu hợp lý** | [Mức cao](./isolation-levels.md) khóa nhiều hơn |

Ví dụ sửa đoạn chuyển khoản ở trên:

```ts
const [first, second] = [fromId, toId].sort((a, b) => a - b);   // luôn khóa theo thứ tự id
await tx.$queryRaw`SELECT 1 FROM acc WHERE id IN (${first}, ${second}) ORDER BY id FOR UPDATE`;
```

## 5. Chẩn đoán

```sql
-- PostgreSQL: ai đang giữ/chờ khóa
SELECT pid, wait_event_type, wait_event, state, query FROM pg_stat_activity WHERE state <> 'idle';
SELECT * FROM pg_locks WHERE NOT granted;

-- MySQL: deadlock gần nhất
SHOW ENGINE INNODB STATUS;   -- xem mục LATEST DETECTED DEADLOCK
```

Bật log deadlock (`log_lock_waits` ở PostgreSQL) để thấy câu lệnh nào dính.

## 6. Deadlock không chỉ có trong DB

- **Luồng/mutex** trong chương trình: hai luồng giữ hai mutex theo thứ tự ngược nhau.
- **Khóa phân tán** (Redis lock) trên nhiều tài nguyên.
- **Pool kết nối cạn:** một request giữ một connection rồi xin thêm connection thứ hai, khi pool hết, tất cả đứng chờ nhau.

Đừng nhầm với [circular dependencies](../be/circular-dependencies.md): đó là vòng phụ thuộc trong *cấu trúc code*, còn deadlock là vòng chờ *lúc chạy*.

## 7. Ghi chú BE và FE

> **BE:** Coi deadlock là sự kiện **bình thường có thể xảy ra**, không phải bug hiếm. Viết sẵn vòng retry (giới hạn 3–5 lần, có jitter) quanh transaction dùng khóa. Giữ thao tác bên ngoài (gọi API, gửi email) tránh xa transaction. Xem [ACID và Transaction](./acid-transaction.md), [Race condition](../be/race-condition.md).

> **FE:** Nếu retry thất bại, BE trả lỗi tạm thời (`503`/`409`). UI hiện thông báo "Hệ thống đang bận, thử lại" và cho phép thử lại, với request ghi thì kèm [Idempotency Key](../be/idempotency-key.md).

## 8. Liên quan

- [Pessimistic lock](./pessimistic-lock.md), [Optimistic lock](./optimistic-lock.md)
- [Isolation levels](./isolation-levels.md)
