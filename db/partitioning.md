# Partitioning

## 1. Định nghĩa kỹ thuật

**Partitioning** (phân vùng) là kỹ thuật chia **một bảng logic lớn thành nhiều phần vật lý nhỏ hơn** gọi là **partition**, dựa trên giá trị của một hoặc nhiều cột gọi là **partition key**. Với ứng dụng, đó vẫn là *một bảng duy nhất*; DB tự định tuyến câu lệnh tới đúng partition.

## 2. Giải thích đơn giản

Tủ hồ sơ có 10 triệu tờ trộn lẫn thì tìm rất lâu. Hãy **chia thành các ngăn theo tháng**: hồ sơ tháng 3 nằm ngăn "Tháng 3". Cần tìm hồ sơ tháng 3 thì chỉ mở một ngăn, bỏ qua 11 ngăn kia. Cuối năm muốn vứt hồ sơ cũ chỉ cần bỏ nguyên ngăn.

```
orders (bảng logic)
 ├── orders_2025_01   (tháng 1)
 ├── orders_2025_02   (tháng 2)
 ├── orders_2025_03   (tháng 3)   ◄── WHERE created_at = '2025-03-10' chỉ chạm ngăn này
 └── ...
```

## 3. Các kiểu phân vùng

| Kiểu | Cách chia | Hợp với |
|---|---|---|
| **Range** | Theo khoảng giá trị (ngày, id) | Dữ liệu theo thời gian, log, đơn hàng |
| **List** | Theo danh sách giá trị rời rạc | Theo quốc gia, khu vực, loại |
| **Hash** | `hash(key) mod N` | Chia đều tải khi không có khoảng tự nhiên |

```sql
-- PostgreSQL: declarative partitioning (range theo tháng)
CREATE TABLE orders (
  id          BIGINT NOT NULL,
  created_at  TIMESTAMPTZ NOT NULL,
  amount      NUMERIC,
  PRIMARY KEY (id, created_at)          -- partition key phải nằm trong khóa chính/unique
) PARTITION BY RANGE (created_at);

CREATE TABLE orders_2025_01 PARTITION OF orders
  FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');
CREATE TABLE orders_2025_02 PARTITION OF orders
  FOR VALUES FROM ('2025-02-01') TO ('2025-03-01');
```

## 4. Lợi ích

1. **Partition pruning:** truy vấn có điều kiện trên partition key chỉ quét các partition liên quan, bỏ qua phần còn lại.
2. **Xóa dữ liệu cũ gần như tức thì:** `DROP`/`DETACH` một partition thay cho `DELETE` hàng triệu dòng, không sinh log khổng lồ và không làm phình bảng.
3. **Bảo trì nhỏ hơn:** `VACUUM`, `REINDEX`, backup chạy từng partition.
4. **Index nhỏ hơn:** mỗi partition có [index](./index-btree.md) riêng nhỏ gọn, dễ nằm gọn trong RAM.
5. **Dễ lưu trữ phân tầng:** partition cũ chuyển sang đĩa rẻ hơn.

```sql
-- kiểm tra pruning
EXPLAIN SELECT * FROM orders WHERE created_at >= '2025-02-10' AND created_at < '2025-02-20';
-- kết quả chỉ nên thấy: Scan on orders_2025_02
```

## 5. Hạn chế và cái giá

- **Điều kiện phải chứa partition key** thì mới được pruning. Truy vấn không có nó sẽ quét **mọi** partition, có khi chậm hơn bảng thường.
- **Ràng buộc `UNIQUE`/`PRIMARY KEY` phải bao gồm partition key**, nên không thể bắt `id` duy nhất toàn cục chỉ bằng khóa unique thông thường.
- Quá nhiều partition (hàng nghìn) làm bước lập kế hoạch truy vấn chậm đi.
- Phải **tạo partition mới trước thời hạn** (ví dụ trước tháng sau), nếu không insert sẽ lỗi. Hãy tự động hóa bằng [batch job](../be/batch-job.md) hoặc extension (`pg_partman`).
- Chọn sai key (dồn hết vào một partition "nóng") thì không có lợi ích.

## 6. Phân biệt các khái niệm hay bị nhầm

| | Partitioning | **Sharding** | Index |
|---|---|---|---|
| Chia ở đâu | Trong **một** DB server | Qua **nhiều** server | Cấu trúc tra cứu bổ sung |
| Mục tiêu | Quản lý dữ liệu lớn, tăng tốc quét | Mở rộng ngang dung lượng và thông lượng | Tìm nhanh từng dòng |
| Phức tạp | Vừa | Cao (join, transaction chéo shard) | Thấp |

Chia thành cột (vertical partitioning) tách các cột ít dùng sang bảng khác là một khái niệm khác, cùng tên gọi chung.

**Khi nào cần:** chỉ khi bảng thực sự rất lớn (hàng trăm triệu dòng hoặc hàng trăm GB), hoặc cần xóa/lưu trữ theo thời gian. Bảng nhỏ và vừa thì [index tốt](./index-btree.md) là đủ, partition chỉ thêm phức tạp.

## 7. Ghi chú BE và FE

> **BE:** Luôn đưa partition key vào điều kiện truy vấn (thường là khoảng thời gian). Phân trang trên bảng đã partition: dùng [cursor pagination](./cursor-pagination.md) kèm điều kiện thời gian, tránh [OFFSET lớn](./offset-pagination.md). Chạy các tác vụ lô theo từng partition ([batch job](../be/batch-job.md)).

> **FE:** Nên bắt người dùng chọn **khoảng thời gian** khi xem lịch sử/báo cáo (mặc định "30 ngày gần nhất"), vừa hợp UX vừa cho phép BE tận dụng pruning. Tìm kiếm không giới hạn thời gian sẽ quét mọi partition.

## 8. Liên quan

- [Index trong Database](./index-btree.md)
- [Cursor Pagination](./cursor-pagination.md), [Offset Pagination](./offset-pagination.md)
- [Batch Job](../be/batch-job.md)
