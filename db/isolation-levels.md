# Isolation Levels và Serializable

## 1. Định nghĩa kỹ thuật

**Isolation level** (mức cô lập) quy định **mức độ một transaction được "nhìn thấy" thay đổi của các transaction khác đang chạy đồng thời**. Đây là chữ **I** trong [ACID](./acid-transaction.md). Chuẩn SQL định nghĩa bốn mức, phân biệt bởi các **hiện tượng bất thường (anomaly)** mà chúng cho phép xảy ra.

**Serializable** là mức cao nhất: kết quả của các transaction chạy đồng thời **tương đương với việc chạy lần lượt từng cái một theo một thứ tự nào đó**.

## 2. Giải thích đơn giản

Mức cô lập giống **mức độ ngăn cách giữa các phòng làm việc**:

- Không ngăn gì: bạn thấy cả bản nháp đồng nghiệp đang viết dở.
- Ngăn dần lên...
- Ngăn hoàn toàn (Serializable): như thể cả văn phòng chỉ có một người làm việc tại một thời điểm.

Ngăn càng chặt thì càng an toàn nhưng càng chậm và dễ phải làm lại.

## 3. Các anomaly

| Anomaly | Mô tả | Ví dụ |
|---|---|---|
| **Dirty read** | Đọc dữ liệu **chưa commit** của transaction khác | T1 sửa giá thành 0 (chưa commit), T2 đọc thấy 0, T1 rollback |
| **Non-repeatable read** | Đọc **cùng một dòng** hai lần ra hai giá trị khác nhau | T1 đọc giá=10, T2 sửa thành 20 và commit, T1 đọc lại thấy 20 |
| **Phantom read** | Chạy **cùng một truy vấn điều kiện** hai lần ra tập dòng khác nhau | T1 `COUNT(*)`=5, T2 chèn thêm một dòng, T1 đếm lại=6 |
| **Lost update** | Hai transaction đọc-sửa-ghi chồng nhau, một cập nhật bị mất | Xem [Race condition](../be/race-condition.md) |
| **Write skew** | Hai transaction đọc cùng dữ liệu, mỗi bên sửa một dòng **khác nhau**, cùng vi phạm một quy tắc chung | Hai bác sĩ trực cùng xin nghỉ, cả hai thấy "còn người khác trực" nên cùng nghỉ, hết người trực |

```
Write skew (quy tắc: luôn có ít nhất 1 bác sĩ trực)
  T1: đếm số bác sĩ trực = 2 ─► OK, cho Alice nghỉ ──► UPDATE Alice
  T2: đếm số bác sĩ trực = 2 ─► OK, cho Bob nghỉ  ──► UPDATE Bob
  Kết quả: 0 người trực. Mỗi transaction đều "đúng" khi nhìn riêng.
```

## 4. Bốn mức theo chuẩn SQL

| Mức | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| Read Uncommitted | Có thể | Có thể | Có thể |
| **Read Committed** | Không | Có thể | Có thể |
| **Repeatable Read** | Không | Không | Có thể (theo chuẩn) |
| **Serializable** | Không | Không | Không |

Thực tế khác nhau giữa các DB:

| DB | Mặc định | Ghi chú |
|---|---|---|
| PostgreSQL | Read Committed | `Read Uncommitted` hành xử như `Read Committed`. `Repeatable Read` là snapshot isolation (không có phantom nhưng còn write skew). `Serializable` dùng **SSI** |
| MySQL InnoDB | Repeatable Read | Dùng MVCC và gap lock |
| SQL Server | Read Committed | Có thêm `SNAPSHOT` |
| Oracle | Read Committed | Không có `Repeatable Read`/`Read Uncommitted`; có `SERIALIZABLE` (thực chất là snapshot isolation) |

## 5. Serializable

Đảm bảo mạnh nhất: loại bỏ **mọi** anomaly kể cả write skew. Có hai cách triển khai:

| Cách | Cơ chế | Hệ quả |
|---|---|---|
| **Khóa** (2PL, strict) | Khóa mọi thứ đã đọc/ghi đến khi kết thúc | Chặn nhau nhiều, dễ [deadlock](./deadlock.md) |
| **Lạc quan** (SSI, PostgreSQL) | Chạy tự do, theo dõi phụ thuộc, **hủy** transaction nếu phát hiện nguy cơ vi phạm | Ít chặn, nhưng sẽ có transaction bị hủy |

Khi bị hủy, DB trả lỗi (PostgreSQL: SQLSTATE `40001` serialization_failure). **Ứng dụng phải bắt lỗi và chạy lại cả transaction.**

```sql
BEGIN ISOLATION LEVEL SERIALIZABLE;
  SELECT count(*) FROM doctors WHERE on_call;
  UPDATE doctors SET on_call = false WHERE name = 'Alice';
COMMIT;        -- có thể lỗi 40001 nếu xung đột với transaction khác → retry
```

```ts
// BE: vòng retry cho Serializable
for (let i = 0; i < 5; i++) {
  try { return await runTransaction(); }
  catch (e) { if (e.code !== '40001') throw e; await sleep(jitter(i)); }
}
```

## 6. Chọn mức nào

| Tình huống | Gợi ý |
|---|---|
| Đa số CRUD thông thường | Read Committed (mặc định) |
| Báo cáo cần số liệu nhất quán trong một lần chạy | Repeatable Read (snapshot) |
| Quy tắc nghiệp vụ phức tạp qua nhiều dòng (kiểu write skew), hoặc không muốn tự lo khóa | **Serializable** + retry |
| Chỉ cần bảo vệ một thao tác cụ thể | Giữ mức thấp, dùng [pessimistic lock](./pessimistic-lock.md) hoặc [optimistic lock](./optimistic-lock.md) cho đúng chỗ |

Nâng isolation toàn hệ thống để chữa một lỗi cục bộ thường đắt đỏ. Khóa đúng chỗ thường hiệu quả hơn.

## 7. Ghi chú BE và FE

> **BE:** Mức cô lập cao nghĩa là **phải có logic retry**. Giữ transaction ngắn để giảm tỷ lệ xung đột. Đặt isolation theo từng transaction thay vì toàn cục khi có thể.

> **FE:** Lỗi xung đột/serialization có thể lọt lên thành `409`/`503`. UI cần thông báo "thử lại" thân thiện và gửi lại bằng [Idempotency Key](../be/idempotency-key.md) để không tạo trùng.

## 8. Liên quan

- [ACID và Transaction](./acid-transaction.md), [Deadlock](./deadlock.md)
- [Pessimistic lock](./pessimistic-lock.md), [Optimistic lock](./optimistic-lock.md)
- [Race condition](../be/race-condition.md)
