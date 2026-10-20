# Cursor Pagination

## 1. Khái niệm

Cursor Pagination (còn gọi là *keyset pagination* / *seek method*) không dùng số trang hay offset. Thay vào đó, client gửi lên một **"con trỏ" (cursor)** — đánh dấu **vị trí bản ghi cuối cùng đã nhận** — và server trả về **N bản ghi nằm sau con trỏ đó**.

> Hiểu đơn giản: "Cho tôi 10 bản ghi **sau bản ghi này**" thay vì "Cho tôi trang số 3".

Cursor thường là giá trị của cột dùng để sắp xếp (vd `id`, `created_at`), thường được **mã hoá base64** để client không phụ thuộc vào cấu trúc bên trong.

---

## 2. Ở tầng Database

### Nguyên lý

Thay vì `OFFSET`, dùng điều kiện `WHERE` để "nhảy" thẳng tới vị trí cursor nhờ **index**.

### Trường hợp 1: sort theo cột unique (vd `id`)

```sql
-- Trang đầu (chưa có cursor)
SELECT id, title, created_at
FROM posts
ORDER BY id DESC
LIMIT 11;            -- lấy limit + 1 để biết còn trang sau không

-- Các trang sau (cursor = id cuối cùng = 90)
SELECT id, title, created_at
FROM posts
WHERE id < 90
ORDER BY id DESC
LIMIT 11;
```

### Trường hợp 2: sort theo cột KHÔNG unique (vd `created_at`) → cursor tổng hợp

Phải thêm cột unique (`id`) làm tie-breaker:

```sql
-- cursor = (created_at = '2026-10-01 10:00:00', id = 90)
SELECT id, title, created_at
FROM posts
WHERE (created_at, id) < ('2026-10-01 10:00:00', 90)   -- row value comparison
ORDER BY created_at DESC, id DESC
LIMIT 11;
```

Nếu DB không hỗ trợ row comparison:

```sql
WHERE created_at < :c_created_at
   OR (created_at = :c_created_at AND id < :c_id)
```

### Index bắt buộc

```sql
CREATE INDEX idx_posts_created_id ON posts (created_at DESC, id DESC);
```

→ DB dùng **index seek** để nhảy thẳng tới vị trí cursor: độ phức tạp ~ `O(log n + limit)`, **không phụ thuộc vị trí trang**.

### MongoDB

```js
// cursor = _id cuối cùng
db.posts
  .find({ _id: { $lt: lastId } })
  .sort({ _id: -1 })
  .limit(11);
```

---

## 3. Ở tầng Backend

### Request (Input)

```
GET /api/posts?limit=10
GET /api/posts?limit=10&cursor=eyJpZCI6OTB9
```

| Tham số  | Kiểu   | Mô tả |
|----------|--------|-------|
| `limit`  | int    | Số bản ghi mỗi lần (giới hạn tối đa) |
| `cursor` | string | Cursor trả về từ lần gọi trước. Không có = lấy từ đầu |

### Response (Output)

```json
{
  "data": [
    { "id": 100, "title": "Post 100" },
    { "id": 99,  "title": "Post 99" }
  ],
  "pageInfo": {
    "nextCursor": "eyJpZCI6OTB9",
    "hasNextPage": true
  }
}
```

- `nextCursor`: cursor của **bản ghi cuối cùng** trong `data`. Nếu hết dữ liệu → `null`.
- Có thể có thêm `prevCursor`, `hasPreviousPage` nếu hỗ trợ lùi.

### Kỹ thuật "lấy limit + 1"

Query `limit + 1` bản ghi:
- Nếu trả về `> limit` → còn trang sau (`hasNextPage = true`), **bỏ bản ghi dư** đi.
- Nếu `<= limit` → hết dữ liệu.

→ Không cần `COUNT(*)`.

### Ví dụ code (Node.js + Prisma)

```ts
const encode = (obj: object) =>
  Buffer.from(JSON.stringify(obj)).toString('base64url');
const decode = (str: string) =>
  JSON.parse(Buffer.from(str, 'base64url').toString());

app.get('/api/posts', async (req, res) => {
  const limit = Math.min(parseInt(req.query.limit as string) || 10, 100);
  const cursor = req.query.cursor
    ? decode(req.query.cursor as string)   // { id: 90 }
    : null;

  const items = await prisma.post.findMany({
    take: limit + 1,                        // lấy dư 1
    ...(cursor && {
      cursor: { id: cursor.id },
      skip: 1,                              // bỏ chính bản ghi cursor
    }),
    orderBy: { id: 'desc' },
  });

  const hasNextPage = items.length > limit;
  const data = hasNextPage ? items.slice(0, limit) : items;
  const last = data[data.length - 1];

  res.json({
    data,
    pageInfo: {
      nextCursor: hasNextPage && last ? encode({ id: last.id }) : null,
      hasNextPage,
    },
  });
});
```

> Lưu ý: `cursor` + `skip: 1` của Prisma bên dưới vẫn sinh SQL kiểu keyset (`WHERE id <= ...`), nên vẫn hiệu quả.

### Lưu ý khi làm Backend

- **Cột sort phải có thứ tự ổn định & có index**, kèm tie-breaker unique (`id`).
- **Validate/giải mã cursor** an toàn (try/catch), trả 400 nếu cursor sai.
- Có thể **ký (sign) cursor** nếu không muốn client tự chỉnh sửa.
- Cursor chỉ có nghĩa với **cùng một bộ filter + sort**; đổi sort/filter → bỏ cursor cũ.

---

## 4. Ở tầng Frontend

- Phù hợp **Infinite Scroll / "Load more"**.
- Không có số trang, **không nhảy tới trang bất kỳ** được.
- Với React Query: `useInfiniteQuery`

```ts
useInfiniteQuery({
  queryKey: ['posts'],
  queryFn: ({ pageParam }) =>
    fetch(`/api/posts?limit=10${pageParam ? `&cursor=${pageParam}` : ''}`)
      .then(r => r.json()),
  initialPageParam: null as string | null,
  getNextPageParam: (lastPage) => lastPage.pageInfo.nextCursor ?? undefined,
});
```

---

## 5. Ưu điểm

| # | Ưu điểm |
|---|---------|
| 1 | **Hiệu năng ổn định**, không chậm dần khi đi sâu (dùng index seek) |
| 2 | **Nhất quán dữ liệu**: không bị trùng/sót khi có bản ghi mới thêm/xoá giữa chừng |
| 3 | Không cần `COUNT(*)` → nhẹ cho DB |
| 4 | Rất hợp **infinite scroll, feed, timeline, chat, log** |
| 5 | Scale tốt với bảng hàng triệu/tỷ bản ghi |

## 6. Nhược điểm

| # | Nhược điểm |
|---|------------|
| 1 | **Không nhảy tới trang bất kỳ** (không có "trang 50") |
| 2 | **Không biết tổng số trang / tổng bản ghi** (trừ khi query riêng) |
| 3 | Phức tạp hơn để cài đặt, nhất là sort nhiều cột / sort cột không unique |
| 4 | Cursor phụ thuộc vào sort + filter; đổi sort là phải reset |
| 5 | Khó **sort theo cột tuỳ ý** (cần index tương ứng cho mỗi kiểu sort) |
| 6 | Việc lùi trang (previous) phức tạp hơn (đảo chiều sort rồi đảo lại kết quả) |
| 7 | Nếu bản ghi cursor bị xoá & dùng kiểu cursor theo "record" (như Prisma `cursor`) có thể lỗi → nên dùng cursor theo **giá trị** (id/created_at) |

---

## 6. Khi nào nên dùng

✅ Nên dùng:
- Infinite scroll, news feed, timeline, notification, chat history.
- Bảng dữ liệu rất lớn.
- Dữ liệu thay đổi liên tục (thêm/xoá thường xuyên).
- API public cần hiệu năng & nhất quán (Twitter, Facebook, Stripe, Slack, GitHub GraphQL đều dùng).

❌ Không nên dùng:
- Cần hiển thị số trang và nhảy trang tuỳ ý.
- Cần biết tổng số bản ghi ngay trên UI.
- Dataset nhỏ, admin table đơn giản.

---

## 7. So sánh nhanh với Offset

| Tiêu chí | Offset | Cursor |
|----------|--------|--------|
| Nhảy tới trang bất kỳ | ✅ | ❌ |
| Biết tổng số trang | ✅ | ❌ (thường) |
| Hiệu năng trang sâu | ❌ Chậm dần | ✅ Ổn định |
| Nhất quán khi dữ liệu đổi | ❌ Trùng/sót | ✅ |
| Độ phức tạp cài đặt | Thấp | Trung bình |
| Hợp infinite scroll | ❌ | ✅ |
| Hợp bảng admin | ✅ | ⚠️ |
