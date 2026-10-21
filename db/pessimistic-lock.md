# Pessimistic Lock

## 1. Định nghĩa kỹ thuật

**Pessimistic locking** (khóa bi quan) là chiến lược kiểm soát đồng thời trong đó transaction **khóa tài nguyên (dòng, bảng) ngay khi đọc** với giả định rằng xung đột *rất có thể xảy ra*. Các transaction khác muốn truy cập xung đột phải **chờ** đến khi khóa được nhả (khi commit/rollback).

## 2. Giải thích đơn giản

Bạn vào phòng thử đồ và **khóa cửa lại** trước, ai muốn vào thì chờ bên ngoài. Chắc chắn không ai chen vào, nhưng người khác phải đứng chờ.

```
T1: SELECT ... FOR UPDATE (khóa dòng #7) ─── sửa ─── COMMIT (nhả khóa)
T2:        muốn sửa dòng #7 ─────── ⏳ chờ ───────────────► rồi mới được vào
```

## 3. Cú pháp

```sql
BEGIN;
  SELECT * FROM products WHERE id = 7 FOR UPDATE;   -- khóa dòng này
  -- kiểm tra tồn kho, tính toán trong app...
  UPDATE products SET stock = stock - 1 WHERE id = 7;
COMMIT;                                              -- nhả khóa
```

| Mệnh đề | Ý nghĩa |
|---|---|
| `FOR UPDATE` | Khóa **độc quyền** các dòng, người khác không sửa/khóa được |
| `FOR SHARE` | Khóa **chia sẻ**: nhiều người đọc-khóa cùng lúc nhưng không ai sửa được |
| `NOWAIT` | Không chờ, nếu dòng đang bị khóa thì **báo lỗi ngay** |
| `SKIP LOCKED` | **Bỏ qua** các dòng đang bị khóa, lấy dòng khác |
| `LOCK TABLE` | Khóa cả bảng (thô, dễ gây nghẽn) |

`SELECT … FOR UPDATE` chỉ có tác dụng **bên trong transaction**. Ngoài transaction, khóa nhả ngay sau câu lệnh.

## 4. Mẫu dùng: hàng đợi công việc với SKIP LOCKED

Nhiều worker cùng lấy job mà không giẫm chân nhau:

```sql
BEGIN;
  SELECT id FROM jobs
  WHERE status = 'PENDING'
  ORDER BY id
  LIMIT 10
  FOR UPDATE SKIP LOCKED;       -- worker khác thấy các dòng này đang khóa → bỏ qua
  -- xử lý và đánh dấu DONE
COMMIT;
```

Xem thêm [Background worker](../be/background-worker.md).

## 5. Advisory lock

Khóa theo **một con số tự đặt** thay vì theo dòng dữ liệu, dùng để "xin quyền" làm một việc (ví dụ chỉ một instance chạy [batch job](../be/batch-job.md)):

```sql
SELECT pg_try_advisory_lock(42);     -- true: giành được; false: đã có người giữ
-- ... làm việc ...
SELECT pg_advisory_unlock(42);
```

## 6. Ưu và nhược điểm

| Ưu | Nhược |
|---|---|
| Đảm bảo mạnh, không bao giờ xung đột ghi | Người khác phải chờ nên giảm thông lượng |
| Logic đơn giản, không cần retry | Giữ khóa lâu làm nghẽn cả hệ thống |
| Hợp khi xung đột thường xuyên | Có nguy cơ [deadlock](./deadlock.md) |
| | Phụ thuộc kết nối DB: user "bỏ đi uống nước" khi đang giữ khóa là thảm họa |

## 7. Khi nào dùng

- Tranh chấp cao trên cùng một dòng (tồn kho "hot", số dư tài khoản phổ biến).
- Thao tác đọc-quyết định-ghi phức tạp, khó gói trong một câu `UPDATE` atomic.
- Chi phí **làm lại** lớn hơn chi phí **chờ**.

Không dùng khi xung đột hiếm thì [optimistic lock](./optimistic-lock.md) nhẹ hơn.

## 8. Quy tắc an toàn

1. **Giữ transaction ngắn.** Không gọi API ngoài, không chờ input người dùng khi đang giữ khóa.
2. **Khóa theo thứ tự nhất quán** (ví dụ luôn khóa dòng có id nhỏ trước) để tránh [deadlock](./deadlock.md).
3. **Đặt timeout** (`lock_timeout`, `innodb_lock_wait_timeout`) để chờ không vô hạn.
4. **Khóa vừa đủ.** Có chỉ mục tốt ([index](./index-btree.md)) thì chỉ khóa đúng dòng cần. Thiếu index, DB có thể khóa cả khoảng lớn hoặc quét toàn bảng.

## 9. Ghi chú BE và FE

> **BE:** Khóa tồn tại cùng connection. Nên bọc trong transaction ở tầng service, và luôn đảm bảo `COMMIT/ROLLBACK` kể cả khi lỗi để không rò rỉ khóa. Xem [Race condition](../be/race-condition.md) để chọn công cụ phù hợp.

> **FE:** Không để UI "giữ khóa" trong lúc người dùng điền form. Đó là khóa *logic* dài ngày. Cách làm tốt hơn là cho phép sửa tự do rồi dùng [optimistic lock](./optimistic-lock.md) khi lưu.

## 10. Liên quan

- [Optimistic lock](./optimistic-lock.md), [Deadlock](./deadlock.md)
- [ACID và Transaction](./acid-transaction.md), [Isolation levels](./isolation-levels.md)
