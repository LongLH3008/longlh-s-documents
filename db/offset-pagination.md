# Offset Pagination

## 1. Khái niệm

Offset Pagination (còn gọi là *page-based pagination*) chia dữ liệu thành các "trang" bằng cách **bỏ qua N bản ghi đầu** (`OFFSET`) rồi **lấy M bản ghi tiếp theo** (`LIMIT`).

- `limit` (hoặc `pageSize`): số bản ghi mỗi trang.
- `offset`: số bản ghi cần bỏ qua = `(page - 1) * limit`.

Ví dụ: `page=3`, `limit=10` → `offset = 20` → lấy bản ghi thứ 21 → 30.

---

## 2. Ở tầng Database

### SQL (PostgreSQL / MySQL)

```sql
-- Lấy trang 3, mỗi trang 10 bản ghi
SELECT id, title, created_at
FROM posts
ORDER BY created_at DESC, id DESC
LIMIT 10 OFFSET 20;

-- Đếm tổng để tính số trang
SELECT COUNT(*) FROM posts;
```

### Cách DB thực thi

1. Duyệt qua dữ liệu theo thứ tự `ORDER BY`.
2. **Đọc và bỏ đi** `OFFSET` dòng đầu tiên.
3. Trả về `LIMIT` dòng tiếp theo.

> ⚠️ DB **vẫn phải quét qua** toàn bộ `OFFSET` dòng bị bỏ. Offset càng lớn → càng chậm (độ phức tạp ~ `O(offset + limit)`).

### Index

- Có index trên cột `ORDER BY` thì sắp xếp nhanh hơn, nhưng **không giải quyết được việc phải skip N dòng**.
- Nên có index: `CREATE INDEX idx_posts_created_id ON posts (created_at DESC, id DESC);`

### MongoDB

```js
db.posts.find().sort({ createdAt: -1 }).skip(20).limit(10);
db.posts.countDocuments();
```

`skip()` cũng có vấn đề hiệu năng tương tự khi skip lớn.

---

## 3. Ở tầng Backend

### Request (Input)

```
GET /api/posts?page=3&limit=10
```

| Tham số | Kiểu | Mặc định | Mô tả |
|---------|------|----------|-------|
| `page`  | int  | 1        | Số trang (bắt đầu từ 1) |
| `limit` | int  | 10       | Số bản ghi mỗi trang (nên giới hạn tối đa, vd 100) |

### Response (Output)

```json
{
  "data": [
    { "id": 21, "title": "Post 21" },
    { "id": 22, "title": "Post 22" }
  ],
  "meta": {
    "page": 3,
    "limit": 10,
    "total": 253,
    "totalPages": 26,
    "hasNext": true,
    "hasPrev": true
  }
}
```

### Ví dụ code (Node.js + Prisma)

```ts
app.get('/api/posts', async (req, res) => {
  const page = Math.max(parseInt(req.query.page as string) || 1, 1);
  const limit = Math.min(parseInt(req.query.limit as string) || 10, 100);
  const skip = (page - 1) * limit;

  const [data, total] = await Promise.all([
    prisma.post.findMany({
      orderBy: [{ createdAt: 'desc' }, { id: 'desc' }],
      skip,
      take: limit,
    }),
    prisma.post.count(),
  ]);

  res.json({
    data,
    meta: {
      page,
      limit,
      total,
      totalPages: Math.ceil(total / limit),
      hasNext: page * limit < total,
      hasPrev: page > 1,
    },
  });
});
```

### Lưu ý khi làm Backend

- **Luôn có `ORDER BY` ổn định** (thêm cột unique như `id` làm tie-breaker), nếu không các trang có thể bị trùng/mất bản ghi.
- Validate và **giới hạn `limit` tối đa**.
- Query `COUNT(*)` có thể chậm trên bảng lớn → cân nhắc cache hoặc ước lượng.

---

## 4. Ở tầng Frontend

- Hiển thị được **số trang** (1, 2, 3, ... 26) và cho phép **nhảy tới trang bất kỳ**.
- Phù hợp UI dạng bảng admin, danh sách có thanh phân trang.
- Với React Query: `queryKey: ['posts', page]`, dùng `placeholderData: keepPreviousData` để mượt khi chuyển trang.

---

## 5. Ưu điểm

| # | Ưu điểm |
|---|---------|
| 1 | **Đơn giản**, dễ hiểu, dễ cài đặt |
| 2 | **Nhảy tới trang bất kỳ** (page 1 → page 50) |
| 3 | Biết **tổng số bản ghi / tổng số trang** |
| 4 | UI phân trang quen thuộc (1 2 3 ... n) |
| 5 | Hỗ trợ tốt bởi hầu hết ORM / framework |
| 6 | Dễ **sort theo bất kỳ cột nào** |

## 6. Nhược điểm

| # | Nhược điểm |
|---|------------|
| 1 | **Chậm khi offset lớn**: DB phải quét và bỏ qua N dòng (trang 10.000 rất chậm) |
| 2 | **Dữ liệu không nhất quán khi có thay đổi**: nếu có bản ghi được thêm/xoá giữa các lần gọi → bị **trùng** hoặc **sót** bản ghi |
| 3 | `COUNT(*)` tốn kém trên bảng lớn |
| 4 | Không phù hợp **infinite scroll / realtime feed** |
| 5 | Tải DB tăng cao khi nhiều người truy cập trang sâu |

### Ví dụ lỗi trùng/sót bản ghi

```
Lúc đầu:  [A B C D E F G H I J K L]   limit = 4
Trang 1:  A B C D
-- Có 1 bản ghi mới "X" được thêm vào đầu --
Danh sách: [X A B C D E F G H I J K L]
Trang 2 (offset 4): D E F G   ← "D" bị TRÙNG
```

Ngược lại, nếu xoá 1 bản ghi ở trang trước thì bản ghi sẽ bị **SÓT**.

---

## 7. Khi nào nên dùng

✅ Nên dùng:
- Trang quản trị (admin), dashboard, bảng dữ liệu.
- Dataset nhỏ-trung bình (vài chục nghìn bản ghi).
- Cần hiển thị số trang & nhảy trang.
- Dữ liệu ít thay đổi.

❌ Không nên dùng:
- Bảng hàng triệu bản ghi và người dùng hay đi sâu.
- Infinite scroll, news feed, chat, timeline.
- Dữ liệu thay đổi liên tục.
