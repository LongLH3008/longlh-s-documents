# Race Condition

## 1. Định nghĩa kỹ thuật

**Race condition** (tình huống đua) xảy ra khi **kết quả của chương trình phụ thuộc vào thứ tự hoặc thời điểm xen kẽ** của nhiều tiến trình/luồng/request cùng truy cập một **tài nguyên dùng chung** mà ít nhất một bên **ghi**, và việc truy cập đó không được đồng bộ hóa đúng cách. Dạng kinh điển là **check-then-act** (kiểm tra rồi mới hành động) hoặc **read-modify-write** (đọc, sửa, ghi).

## 2. Giải thích đơn giản

Tài khoản có 100k. Hai người cùng lúc rút 80k ở hai ATM. Cả hai máy đều **đọc** thấy "còn 100k, đủ", rồi cùng trừ. Kết quả: rút được 160k, trong khi chỉ có 100k.

```
Thời gian ─►
Request A:  đọc balance=100 ─────────────► ghi balance=20
Request B:        đọc balance=100 ─────────────► ghi balance=20
                        ▲ cả hai đọc trước khi bên kia ghi
Kết quả: balance=20, nhưng đã chi 160. Mất một lần trừ (lost update).
```

Lỗi này **không xảy ra mọi lần**, chỉ khi hai request trùng thời điểm, nên khó tái hiện và hay lọt vào production.

## 3. Các dạng thường gặp trong backend

| Dạng | Ví dụ |
|---|---|
| **Lost update** | Hai request cùng cộng/trừ số dư, tồn kho, bộ đếm |
| **Check-then-act** | "Nếu username chưa tồn tại thì tạo", hai request cùng thấy chưa tồn tại |
| **Double submit** | User bấm "Thanh toán" hai lần, hoặc retry tạo đơn trùng |
| **Oversell** | Hai khách cùng mua món hàng cuối cùng |
| **Cache race** | Ghi cache giá trị cũ đè lên giá trị mới ([Cache](./cache.md)) |
| **Job chạy trùng** | Hai worker cùng nhặt một job ([Background worker](./background-worker.md)) |

## 4. Cách phòng chống

Chọn theo phạm vi, ưu tiên cách đơn giản và gần dữ liệu nhất.

### 4.1 Thao tác atomic ngay trong DB (nên thử trước)

```sql
-- SAI: đọc rồi ghi ở app
SELECT balance FROM acc WHERE id=1;   -- app tính balance-80
UPDATE acc SET balance = 20 WHERE id=1;

-- ĐÚNG: một câu lệnh duy nhất, DB tự khóa dòng
UPDATE acc SET balance = balance - 80 WHERE id = 1 AND balance >= 80;
-- kiểm tra số dòng bị ảnh hưởng: 0 → không đủ tiền
```

### 4.2 Ràng buộc dữ liệu

`UNIQUE`, `CHECK (stock >= 0)`, khóa ngoại. Để DB là chốt chặn cuối cùng, không dựa vào "kiểm tra trước ở code".

### 4.3 Khóa

- [Pessimistic lock](../db/pessimistic-lock.md): khóa dòng trước khi sửa (`SELECT … FOR UPDATE`). Hợp khi xung đột thường xuyên.
- [Optimistic lock](../db/optimistic-lock.md): cột `version`, ghi thất bại nếu version đã đổi. Hợp khi xung đột hiếm.
- Khóa phân tán (Redis lock) khi dữ liệu nằm ngoài một DB. Cần TTL và fencing token.

### 4.4 Mức cô lập transaction

[Isolation level](../db/isolation-levels.md) cao như `Serializable` ngăn các anomaly phức tạp (write skew), đổi lại phải xử lý retry.

### 4.5 Tuần tự hóa bằng hàng đợi

Đưa mọi thao tác trên cùng một thực thể vào một queue theo key, mỗi key chỉ một consumer xử lý ([Queue và Event](./queue-event.md)).

### 4.6 Idempotency

Chặn double submit bằng [Idempotency Key](./idempotency-key.md).

| Cách | Phù hợp khi |
|---|---|
| Atomic UPDATE / constraint | Phần lớn trường hợp, ưu tiên số 1 |
| Optimistic lock | Xung đột ít, muốn giữ throughput |
| Pessimistic lock | Xung đột nhiều, thao tác phức tạp |
| Serializable | Quy tắc nghiệp vụ liên quan nhiều dòng |
| Khóa phân tán | Tài nguyên ngoài DB |
| Queue theo key | Cần thứ tự nghiêm ngặt |

## 5. Ghi chú FE

> **FE cũng có race condition**, điển hình:
> - **Double click:** vô hiệu hóa nút khi đang gửi, nhưng đó chỉ là UX, BE vẫn phải tự bảo vệ.
> - **Phản hồi về sai thứ tự:** gõ "ab" rồi "abc", response của "ab" về sau và đè kết quả "abc". Giải pháp: hủy request cũ bằng `AbortController`, hoặc chỉ nhận kết quả của request mới nhất. Giảm bớt request bằng [debounce](../fe/debounce-throttle.md).
> - **Nhiều request refresh token cùng lúc:** chỉ cho một refresh chạy, các request khác chờ cùng một promise ([JWT](./auth-jwt-token-version.md)).

## 6. Lỗi hay gặp

- Tin vào "kiểm tra ở app trước rồi mới ghi" mà không có ràng buộc ở DB.
- Dùng khóa trong bộ nhớ của một instance nhưng chạy nhiều instance.
- Giữ khóa quá lâu hoặc khóa theo thứ tự khác nhau, dẫn tới [deadlock](../db/deadlock.md).

## 7. Liên quan

- [Deadlock](../db/deadlock.md), [ACID và Transaction](../db/acid-transaction.md)
- [Idempotency Key](./idempotency-key.md)
