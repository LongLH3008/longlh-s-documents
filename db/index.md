# Database

> Mục lục các kỹ thuật database.

## Tài liệu

### Truy vấn và cấu trúc dữ liệu

- [Index trong Database](./index-btree.md): khái niệm, B-Tree, page split, composite index, EXPLAIN
- [Partitioning](./partitioning.md): range/list/hash, partition pruning, khác với sharding
- [Offset Pagination](./offset-pagination.md): phân trang bằng LIMIT/OFFSET
- [Cursor Pagination](./cursor-pagination.md): phân trang bằng con trỏ (keyset)

### Transaction và đồng thời

- [ACID và Transaction](./acid-transaction.md): atomicity, consistency, isolation, durability, WAL, savepoint
- [Isolation Levels và Serializable](./isolation-levels.md): anomaly, bốn mức cô lập, Serializable và retry
- [Pessimistic Lock](./pessimistic-lock.md): SELECT FOR UPDATE, SKIP LOCKED, advisory lock
- [Optimistic Lock](./optimistic-lock.md): cột version, ETag/If-Match, xử lý xung đột
- [Deadlock](./deadlock.md): điều kiện Coffman, cách DB xử lý, phòng tránh bằng thứ tự khóa
