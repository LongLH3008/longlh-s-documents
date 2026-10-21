# Cache

## 1. Định nghĩa kỹ thuật

**Cache** là một lớp lưu trữ **tạm thời, truy cập nhanh** chứa bản sao của dữ liệu đắt đỏ để lấy (tính toán nặng, truy vấn DB, gọi mạng), nhằm phục vụ các lần truy cập sau nhanh hơn. Một lần truy cập tìm thấy dữ liệu trong cache là **cache hit**, không thấy là **cache miss**.

```
hit ratio = hit / (hit + miss)
```

## 2. Giải thích đơn giản

Bạn hay tra một số điện thoại trong danh bạ dày. Thay vì lần nào cũng lật sách, bạn **ghi lên tờ giấy nhớ** dán cạnh bàn. Lần sau nhìn giấy nhớ là xong. Cái khó là: số điện thoại đổi mà giấy nhớ vẫn ghi số cũ, tức dữ liệu **cũ (stale)**. Đây là lý do có câu *"chỉ có hai việc khó trong khoa học máy tính: cache invalidation và đặt tên."*

## 3. Các lớp cache

```
Browser ─► CDN/Edge ─► Reverse proxy ─► App (in-memory) ─► Redis ─► DB (buffer pool)
 nhanh nhất ◄──────────────────────────────────────────────────────► chậm nhất
```

| Lớp | Ví dụ |
|---|---|
| Browser | HTTP cache, Service Worker |
| CDN / Edge | Ảnh, JS, CSS, cả HTML tĩnh ([edge-cdn.md](./edge-cdn.md)) |
| Ứng dụng | Biến trong RAM, `Map`, LRU cache |
| Cache phân tán | Redis, Memcached |
| DB | Buffer pool, query cache |

## 4. Các chiến lược đọc/ghi

### Cache-aside (lazy loading), phổ biến nhất

```
đọc:  cache có? ─ có ─► trả về
              └ không ─► đọc DB ─► ghi vào cache (kèm TTL) ─► trả về
ghi:  ghi DB ─► XÓA key trong cache (không phải cập nhật)
```

### Các kiểu khác

| Kiểu | Cách hoạt động | Ghi chú |
|---|---|---|
| Read-through | Cache tự tải từ DB khi miss | Code ứng dụng đơn giản hơn |
| Write-through | Ghi cache và DB cùng lúc | Nhất quán, ghi chậm hơn |
| Write-behind | Ghi cache trước, ghi DB sau (bất đồng bộ) | Nhanh, nhưng có thể **mất dữ liệu** nếu cache chết |

## 5. Hết hạn và đẩy bỏ (eviction)

- **TTL:** mỗi key có thời gian sống. Là cách đơn giản nhất để chặn dữ liệu cũ vô hạn.
- **LRU** (ít dùng gần đây nhất bị loại), **LFU** (ít dùng nhất bị loại), **FIFO**: chính sách khi cache đầy.
- **Invalidation chủ động:** xóa key khi dữ liệu gốc đổi. Dễ sót, vì vậy luôn kèm TTL làm chốt an toàn.

## 6. Ba sự cố kinh điển

| Sự cố | Mô tả | Cách chống |
|---|---|---|
| **Cache stampede** (thundering herd) | Key nóng hết hạn, hàng nghìn request cùng miss và cùng đập vào DB | Khóa khi tải lại (chỉ một request đi tải), "stale-while-revalidate", gia hạn sớm |
| **Cache penetration** | Truy vấn key **không tồn tại** nên luôn miss, đánh thẳng DB | Cache cả kết quả rỗng (TTL ngắn), Bloom filter, [rate limit](./rate-limit.md) |
| **Cache avalanche** | Nhiều key hết hạn cùng lúc | Cộng thêm độ lệch ngẫu nhiên (jitter) vào TTL |

```
Stampede:
  TTL hết ─► 1000 request cùng miss ─► 1000 truy vấn DB cùng lúc ─► DB quá tải
Cách chữa: chỉ 1 request giữ khóa đi tải DB, 999 request còn lại chờ hoặc dùng bản cũ
```

## 7. Cache và tính nhất quán

Cache-aside có một cửa sổ race: một request đọc DB giá trị cũ, trong lúc đó request khác ghi DB và xóa cache, rồi request đầu ghi giá trị cũ vào cache. Đây là một dạng [race condition](./race-condition.md). Giảm thiểu bằng TTL ngắn, hoặc xóa cache hai lần (delayed double delete). Với dữ liệu tài chính cần nhất quán tuyệt đối thì đọc thẳng DB.

## 8. Ghi chú FE và DB

> **FE:** HTTP cache điều khiển bằng header:
> - `Cache-Control: max-age=31536000, immutable` cho file có hash tên (`app.3f9a.js`).
> - `Cache-Control: no-cache` + `ETag` cho dữ liệu cần xác thực lại (server trả `304 Not Modified` nếu chưa đổi).
> - Thư viện như React Query/SWR cache phía client theo kiểu *stale-while-revalidate*.

> **DB:** Đừng dùng cache để che truy vấn chậm mà quên [index](../db/index-btree.md). Cache giảm tải đọc, còn ghi vẫn phải xuống DB. Với truy vấn nặng lặp lại có thể dùng materialized view.

## 9. Lỗi hay gặp

- Cache dữ liệu theo người dùng với key chung, rò rỉ dữ liệu giữa user.
- Không có TTL, nên cache cũ tồn tại mãi.
- Cache thứ vốn đã nhanh, chỉ thêm độ phức tạp.
- Coi cache là nguồn dữ liệu gốc (cache có thể mất bất cứ lúc nào).

## 10. Liên quan

- [Edge và CDN](./edge-cdn.md), [Rate limit](./rate-limit.md)
- [Auth Opaque token](./auth-opaque-token-sliding-session.md): session lưu trong Redis.
