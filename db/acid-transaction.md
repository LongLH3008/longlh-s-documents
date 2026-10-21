# ACID và Transaction

## 1. Định nghĩa kỹ thuật

**Transaction** là một **đơn vị công việc logic** gồm một hoặc nhiều thao tác đọc/ghi mà DBMS đảm bảo được thực hiện **như một khối không chia nhỏ**: hoặc thành công toàn bộ (**commit**), hoặc không để lại dấu vết nào (**rollback**).

**ACID** là bốn thuộc tính mà một transaction đáng tin cậy phải có:

| Chữ | Tên | Phát biểu |
|---|---|---|
| **A** | Atomicity (nguyên tử) | Tất cả hoặc không gì cả |
| **C** | Consistency (nhất quán) | Transaction đưa DB từ trạng thái hợp lệ này sang trạng thái hợp lệ khác (mọi ràng buộc được giữ) |
| **I** | Isolation (cô lập) | Các transaction chạy đồng thời không "thấy" trạng thái dở dang của nhau |
| **D** | Durability (bền vững) | Đã commit là không mất, kể cả khi mất điện |

## 2. Giải thích đơn giản

Chuyển 100k từ tài khoản A sang B gồm hai bước: trừ A, cộng B. Nếu hệ thống sập **giữa hai bước**, tiền biến mất. Transaction đảm bảo: hoặc xong cả hai, hoặc như chưa từng có gì.

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100000 WHERE id = 'A';
  UPDATE accounts SET balance = balance + 100000 WHERE id = 'B';
COMMIT;        -- cả hai có hiệu lực cùng lúc
-- gặp lỗi bất kỳ:  ROLLBACK;  → trả về trạng thái ban đầu
```

## 3. Từng chữ trong ACID

### A: Atomicity

```
BEGIN ─► trừ A ─► (sập!) ─► khôi phục: hoàn tác trừ A ─► như chưa có gì
```

DB ghi trước thay đổi vào **log** để có thể hoàn tác (undo) những gì đã làm dở.

### C: Consistency

Do **cả DB và ứng dụng** cùng đảm bảo. DB thi hành các ràng buộc (`PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE`, `CHECK (balance >= 0)`). Quy tắc nghiệp vụ phức tạp hơn (tổng tiền không đổi sau chuyển khoản) do logic của bạn đảm bảo. Đây là chữ "mềm" nhất trong bốn chữ.

### I: Isolation

Hai transaction chạy song song nhưng kết quả phải như thể chạy lần lượt. Triển khai bằng khóa ([pessimistic lock](./pessimistic-lock.md)) hoặc **MVCC** (mỗi transaction đọc một ảnh chụp dữ liệu nhất quán). Cô lập hoàn hảo thì tốn kém, nên SQL cho chọn nhiều mức: [Isolation levels](./isolation-levels.md).

### D: Durability

DB dùng **WAL (Write-Ahead Log)**: ghi thay đổi vào log trên đĩa **trước** khi báo "commit thành công". Khi sập, khởi động lại sẽ replay log để khôi phục.

```
UPDATE ─► ghi WAL xuống đĩa (fsync) ─► báo COMMIT OK ─► dữ liệu thật ghi dần xuống đĩa sau
              ▲ đây là điểm "bền vững"
```

## 4. Savepoint

Rollback một phần mà không hủy cả transaction:

```sql
BEGIN;
  INSERT INTO orders ...;
  SAVEPOINT sp1;
    INSERT INTO coupon_usage ...;     -- lỗi
  ROLLBACK TO sp1;                    -- bỏ riêng phần coupon
COMMIT;                               -- order vẫn được lưu
```

## 5. Thực hành tốt khi dùng transaction

1. **Giữ ngắn.** Transaction dài giữ khóa lâu, chặn người khác và tăng nguy cơ [deadlock](./deadlock.md).
2. **Không gọi API ngoài / gửi email trong transaction.** Việc đó không rollback được và kéo dài thời gian giữ khóa. Hãy đưa vào [queue](../be/queue-event.md) sau khi commit (dùng outbox pattern).
3. **Luôn xử lý lỗi và rollback**, đừng để connection kẹt ở trạng thái đang mở.
4. **Chọn đúng mức isolation** và sẵn sàng retry khi bị hủy do xung đột.
5. Thao tác chỉ một dòng thì một câu `UPDATE` đã là atomic, không cần bọc thêm.

## 6. Ghi chú BE và FE

> **BE:** Transaction thường được quản lý ở tầng service (`@Transactional`, `prisma.$transaction`, `knex.transaction`). Những thao tác nằm trong nhiều DB/service khác nhau không dùng chung được transaction, cần **saga** hoặc **outbox** (xem [Queue và Event](../be/queue-event.md)). Để chặn xung đột ghi chồng, xem [Race condition](../be/race-condition.md).

> **FE:** FE không điều khiển transaction. Nhưng khi BE trả lỗi xung đột/serialization, UI nên cho phép thử lại, và request ghi nên đi kèm [Idempotency Key](../be/idempotency-key.md) để retry an toàn.

## 7. Lưu ý

- ACID là đặc trưng của DB quan hệ. Nhiều NoSQL đánh đổi một phần ACID để lấy khả năng mở rộng (mô hình **BASE**: Basically Available, Soft state, Eventually consistent), tuy một số đã hỗ trợ transaction trong phạm vi nhất định.
- `COMMIT` thành công không bảo đảm đã được replica nhận, trừ khi cấu hình replication đồng bộ.

## 8. Liên quan

- [Isolation levels và Serializable](./isolation-levels.md)
- [Pessimistic lock](./pessimistic-lock.md), [Optimistic lock](./optimistic-lock.md), [Deadlock](./deadlock.md)
