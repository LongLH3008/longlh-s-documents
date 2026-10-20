# Index trong Database

> Tài liệu tổng hợp: khái niệm, bản chất, cấu trúc B-Tree và quá trình phân chia page.

---

## 1. Index là gì?

**Index** là một cấu trúc dữ liệu phụ, nằm tách biệt với bảng, giúp tìm bản ghi nhanh mà không phải quét toàn bộ bảng.

- **Không có index**: full table scan, đọc lần lượt mọi page của bảng, độ phức tạp **O(n)**.
- **Có index (B-Tree)**: độ phức tạp **O(log n)**. Bảng 1 triệu dòng chỉ cần khoảng 3-4 lần đọc page.

```sql
CREATE INDEX idx_users_email ON users(email);
```

**Định nghĩa chính xác:** index là **bản sao đã được sắp xếp của một (hoặc vài) cột, kèm địa chỉ để quay về dòng gốc**.

---

## 2. Dữ liệu nằm ở đâu? (Nền tảng)

Database cuối cùng chỉ là **các file trên đĩa**, chia thành các khối bằng nhau gọi là **page**:

| Hệ quản trị | Kích thước page mặc định |
|---|---|
| PostgreSQL, SQL Server | 8KB |
| MySQL InnoDB | 16KB |

- Đĩa đọc theo **page**, không đọc từng dòng. Chi phí chính của truy vấn là **số page phải đọc (I/O)**.
- **Bảng (heap)** = tập hợp page phẳng, dòng nằm theo thứ tự thêm vào, không có quy luật theo cột nào.
- **Index** = tập hợp page **riêng**, là dữ liệu thật, chiếm dung lượng thật.

Vì dòng trong bảng nằm lộn xộn, tìm `email = 'minh@x.com'` mà không có index thì không có manh mối nào để đoán nó ở page nào, buộc phải đọc **hết**.

---

## 3. Index lưu gì?

`CREATE INDEX idx_email ON users(email)` tạo ra **1 index duy nhất** cho cột đó, bên trong chứa **1 entry cho mỗi dòng** của bảng.

| | Số lượng |
|---|---|
| Index (cả cấu trúc) | 1 |
| Entry trong index | 1 entry / 1 dòng của bảng |

Mỗi entry = **(giá trị cột, địa chỉ dòng)**, xếp theo giá trị cột. Địa chỉ dòng có dạng `(số page, ô trong page)`.

**Bảng `users`** (xếp theo thứ tự thêm vào):

| địa chỉ dòng | id | email | tên |
|---|---|---|---|
| (page 1, ô 1) | 101 | tuan@x.com | Tuấn |
| (page 1, ô 2) | 102 | an@x.com | An |
| (page 1, ô 3) | 103 | minh@x.com | Minh |
| (page 2, ô 1) | 104 | binh@x.com | Bình |

**Index trên `email`** (xếp a→z):

| email | địa chỉ dòng |
|---|---|
| an@x.com | (page 1, ô 2) |
| binh@x.com | (page 2, ô 1) |
| minh@x.com | (page 1, ô 3) |
| tuan@x.com | (page 1, ô 1) |

- Index **chỉ chứa cột được index + địa chỉ**, không chứa các cột khác, nên mỗi entry rất nhỏ.
- Thêm index thứ hai (ví dụ trên `phone`) là thêm **1 cấu trúc nữa**, cũng N entry nhưng xếp theo phone.
- Mỗi lần INSERT/UPDATE/DELETE phải cập nhật **mọi** index liên quan, đó là lý do index làm chậm ghi.

---

## 4. Vì sao có thứ tự thì tìm nhanh?

Khi dữ liệu **đã sắp xếp**, mỗi lần so sánh loại được một phần lớn dữ liệu mà không cần mở chúng. Giống tra từ điển giấy: mở giữa cuốn, thấy chữ "M" thì biết chữ "T" nằm phía sau, cả nửa trước khỏi đọc.

Nếu dữ liệu lộn xộn thì không loại trừ được gì, buộc phải xem hết.

Vì bảng chỉ sắp theo được một cách, muốn tìm nhanh theo nhiều cột thì mỗi cột cần một bản sắp xếp riêng. Đó chính là mỗi index.

---

## 5. B-Tree là gì?

**B-Tree không phải thứ riêng nằm đâu đó. Nó là cách tổ chức các page của index thành hình cây.** Bản thân index chính là B-Tree.

- Sinh ra khi chạy `CREATE INDEX` (hoặc khi tạo PRIMARY KEY / UNIQUE).
- Nằm trong file của database trên đĩa, do database engine tự duy trì.

### Vì sao không dùng binary search thuần trên mảng sắp xếp?

| Vấn đề | Giải thích |
|---|---|
| Mỗi lần đọc đĩa lấy cả page | Binary search mỗi bước chỉ dùng vài byte, lãng phí; 20 bước = 20 lần đọc đĩa |
| Mảng khó cập nhật | Chèn vào giữa phải dịch cả triệu phần tử |

**B-Tree giải quyết bằng cách:** mỗi node = 1 page, nhồi **hàng trăm mốc** vào mỗi page, nên mỗi lần đọc chia dữ liệu thành hàng trăm nhánh thay vì 2. Chèn/xóa chỉ ảnh hưởng cục bộ.

### Bảng phẳng vs index dạng cây

```
BẢNG (heap, phẳng)               INDEX (B-Tree)

[Page 1][Page 2][Page 3]...            [ROOT]
                                      /   |   \
 dòng lẫn lộn,                    [mid] [mid] [mid]
 không thứ tự                      / \    / \    / \
                                 [lá][lá][lá][lá][lá][lá]
                                   └────┴────┴──► địa chỉ dòng trong bảng
```

Lưu ý: với **clustered index** (MySQL InnoDB, SQL Server), chính bảng cũng là một B-Tree theo Primary Key, tầng lá chứa nguyên dòng dữ liệu.

---

## 6. Cấu trúc B+Tree

| Loại page | Chứa gì |
|---|---|
| **Root** | Mốc chỉ đường tới các page con, là điểm bắt đầu mọi truy vấn |
| **Internal (tầng giữa)** | Mốc chỉ đường tới page ở tầng dưới |
| **Leaf (lá)** | Entry thật: (giá trị cột, địa chỉ dòng). Các lá nối với nhau theo thứ tự |

- Mọi tầng đều có cùng bản chất: **danh sách mốc đã sắp xếp, mỗi mốc trỏ xuống một page tầng dưới.**
- **Mốc** là **cận dưới / ranh giới** của khoảng giá trị mà page con phụ trách, thường là **key của entry đầu tiên** trong page con.
- Các lá nối nhau bằng linked list, nên truy vấn range (`BETWEEN`, `ORDER BY`) chỉ cần tìm điểm đầu rồi đi dọc theo lá.
- Cây **cân bằng**: mọi lá luôn cùng độ sâu, nên mọi truy vấn có chi phí gần như nhau.

---

## 7. Xây cây từ dưới lên (chia ban đầu)

**Cơ sở chia:** thứ tự giá trị + sức chứa của page. Dữ liệu đã sắp xếp, cứ đầy một page thì sang page kế. Không có tiêu chí nào khác.

**Ví dụ:** 12 email, mỗi page chứa tối đa 3 mục.

`an, binh, chi, dung, hoa, lan, minh, nam, oanh, phuc, quan, tuan`

**Bước 1. Tầng lá:** cắt dữ liệu đã sắp xếp, mỗi 3 entry một page.

```
L1:[an,binh,chi]  L2:[dung,hoa,lan]  L3:[minh,nam,oanh]  L4:[phuc,quan,tuan]
```

**Bước 2. Tầng 1:** lấy **entry đầu của mỗi page lá** làm mốc.

```
L1 → an,  L2 → dung,  L3 → minh,  L4 → phuc
```

4 mốc nhưng mỗi page chứa tối đa 3, nên chia thành 2 page chỉ đường:

```
M1:[ an→L1 | dung→L2 | minh→L3 ]      M2:[ phuc→L4 ]
```

**Bước 3. Root:** lấy mốc đầu của M1, M2.

```
                    ROOT
              [ an  |  phuc ]
              /            \
      M1:[an|dung|minh]    M2:[phuc]
       /     |     \          |
     L1     L2     L3        L4
```

**Quy luật lặp ở mọi tầng:** cắt theo sức chứa, lấy mốc đầu của mỗi page, đưa lên tầng trên. Dừng khi chỉ còn 1 page: đó là root.

---

## 8. Tìm kiếm trong cây

Ở mỗi tầng: chọn **mốc lớn nhất mà vẫn ≤ giá trị cần tìm**, đi xuống page đó.

Tìm `nam`:

| Bước | Hành động | Đọc page |
|---|---|---|
| 1 | ROOT: `an ≤ nam < phuc` → đi M1 | 1 |
| 2 | M1: `minh ≤ nam` → đi L3 | 2 |
| 3 | L3: thấy `nam` → lấy địa chỉ dòng | 3 |
| 4 | Nhảy tới page của bảng, lấy dòng đầy đủ | 4 |

Phần lớn page không hề bị đọc. Đó là "không quét toàn bộ".

---

## 9. Chèn dữ liệu và tách page (page split)

**Quy tắc: page chỉ tách khi nó đầy mà vẫn cần chèn thêm.**

1. Tìm lá phù hợp theo thứ tự (như tìm kiếm).
2. **Lá còn chỗ** → chèn thẳng, xong, không tách gì.
3. **Lá đầy** → tách:
   - Tạo page mới.
   - Chia đôi entry (kèm entry mới) cho hai page.
   - Đưa **mốc mới** (entry đầu của page mới) lên page cha.
4. **Page cha cũng đầy** → cha tách tiếp, đẩy mốc lên tầng trên. Lặp lại.
5. **Root đầy** → root tách đôi, tạo **root mới** phía trên. Cây cao thêm 1 tầng.

**Ví dụ:** chèn `cuong` vào L1 `[an, binh, chi]` đang đầy (3/3).

```
Trước:  L1: [an, binh, chi]                      ← đầy

Sau:    L1:  [an, binh]
        L1b: [chi, cuong]                        ← page mới
        Cha: thêm mốc "chi" → L1b
```

**Vì sao cây luôn cân bằng:** cây chỉ cao thêm ở **đỉnh** (khi root tách), không bao giờ ở một nhánh riêng lẻ, nên mọi lá luôn cùng độ sâu.

**Xóa:** entry bị xóa khỏi lá. Mốc ở tầng trên thường **không sửa ngay**, vì nó là ranh giới phân vùng chứ không phải bản sao của một bản ghi còn tồn tại, nên vẫn dẫn đường đúng. Page quá thưa có thể được gộp lại.

**Rút gọn mốc:** nhiều database chỉ lưu phần đủ để phân biệt hai page kề nhau (ví dụ `m` thay vì `minh@x.com`) để nhồi được nhiều mốc hơn một page.

---

## 10. Một page chứa bao nhiêu?

> **Số dòng/entry trong 1 page ≈ (kích thước page − header) ÷ kích thước 1 dòng/entry**

| | Kích thước | Số lượng / page 16KB |
|---|---|---|
| Dòng bảng (~140 byte) | rộng | ~110 dòng |
| Entry index email (~40 byte) | nhỏ | ~400 entry |
| Entry index INT (~16 byte) | rất nhỏ | ~700-1000 entry |

Page thường không đầy 100%, mà khoảng **70-90%** để chừa chỗ chèn.

**Áp vào bảng 1 triệu dòng:**

| | Số page |
|---|---|
| Bảng | 1.000.000 ÷ 110 ≈ **9.100 page** |
| Lá của index email | 1.000.000 ÷ 400 ≈ **2.500 page** |
| Tầng giữa | ~7 page |
| Root | 1 page |

Cây chỉ cao **3 tầng**. Tìm 1 email = 3 lần đọc index + 1 lần đọc bảng = **4 lần**, thay vì 9.100 lần.

**Số tầng theo sức chứa (500 mốc/page):**

| Số tầng | Số bản ghi quản lý được |
|---|---|
| 1 | 500 |
| 2 | 250.000 |
| 3 | 125.000.000 |
| 4 | ~62 tỷ |

---

## 11. Unique có liên quan không?

**Index và tính duy nhất là hai thứ độc lập.**

- Index **không cần unique**: cột `status` trùng hàng triệu lần vẫn index được, B-Tree lưu nhiều entry cùng key.
- **Unique index = index + ràng buộc không trùng.** Database dùng chính B-Tree để kiểm tra trùng (tra cây xem key đã có chưa). Nên ràng buộc UNIQUE thực tế được cài đặt bằng index.
- **Primary Key** = unique + not null, thường gắn với clustered index.
- Cột ít trùng (email, id) → index hiệu quả hơn. Cột ít giá trị (gender) → index trỏ tới nửa bảng, gần như vô dụng.

---

## 12. Clustered vs Non-clustered

| | Clustered | Non-clustered (secondary) |
|---|---|---|
| Lá chứa | Nguyên dòng dữ liệu | Key + địa chỉ dòng (InnoDB: giá trị PK) |
| Số lượng / bảng | 1 | Nhiều |
| Ví dụ | PK của InnoDB, SQL Server | Index trên email |

- PostgreSQL dùng heap, không có clustered index thực sự.
- Tra qua secondary index trong InnoDB: tìm PK ở secondary index → tra tiếp cây clustered để lấy dòng (key lookup, tốn thêm I/O).
- Nên chọn PK **ngắn, tăng dần**. UUID v4 ngẫu nhiên gây page split và phình mọi secondary index; dùng UUID v7 / ULID thay thế.

---

## 13. Các loại index khác

| Loại | Dùng cho |
|---|---|
| Hash | Chỉ `=`, rất nhanh, không range/sort |
| GIN | Mảng, JSONB, full-text (PostgreSQL) |
| GiST / R-Tree | Dữ liệu hình học, địa lý |
| BRIN | Bảng cực lớn, dữ liệu xếp tự nhiên (timestamp) |
| Full-text | Tìm kiếm văn bản |
| Bitmap | Cột ít giá trị phân biệt (data warehouse) |
| LSM-Tree | Ghi cực nhiều (Cassandra, RocksDB) |

Theo chức năng: **đơn cột**, **unique**, **composite** (nhiều cột), **covering** (chứa đủ cột truy vấn cần, không phải đọc bảng), **partial** (chỉ một phần dòng), **expression** (trên `LOWER(email)`).

---

## 14. Composite index và leftmost prefix

```sql
CREATE INDEX idx_abc ON t(a, b, c);
```

Sắp xếp theo `a`, trong cùng `a` theo `b`, trong cùng `b` theo `c` (như danh bạ: Họ, rồi Tên đệm, rồi Tên).

| Truy vấn | Dùng index? |
|---|---|
| `WHERE a = 1` | ✅ |
| `WHERE a = 1 AND b = 2` | ✅ |
| `WHERE b = 2` | ❌ bỏ qua cột đầu |
| `WHERE a = 1 AND c = 3` | ⚠️ chỉ dùng phần `a` |
| `WHERE a = 1 AND b > 5 AND c = 3` | ⚠️ dùng `a`, `b`; `c` sau range không tận dụng được |

**Thứ tự cột:** cột `=` đặt trước, cột range đặt sau, cột `ORDER BY` đặt cuối.

---

## 15. Khi nào index không được dùng (sargable)

```sql
-- ❌ Hàm trên cột            ✅ Viết lại
WHERE YEAR(created_at) = 2026     WHERE created_at >= '2026-01-01' AND created_at < '2027-01-01'
-- ❌ Wildcard đầu            ✅ Prefix
WHERE name LIKE '%an'             WHERE name LIKE 'An%'
-- ❌ Ép kiểu ngầm            ✅
WHERE phone = 0912345678          WHERE phone = '0912345678'
-- ❌ Tính toán trên cột      ✅
WHERE price * 2 > 100             WHERE price > 50
```

Optimizer cũng cố ý bỏ qua index khi: bảng quá nhỏ, truy vấn lấy phần lớn bảng (>20-30%), cột selectivity thấp, hoặc thống kê đã lỗi thời.

---

## 16. Cái giá của index

| Chi phí | Giải thích |
|---|---|
| Chậm ghi | Mỗi INSERT/UPDATE/DELETE cập nhật mọi index liên quan |
| Tốn dung lượng | Mỗi index là một bản sao một phần dữ liệu |
| Tốn RAM | Index muốn nhanh phải nằm trong buffer pool |
| Phân mảnh | Page split, xóa nhiều → cần rebuild |

Bảng đọc nhiều: index nhiều hơn. Bảng ghi nhiều (log, event): index càng ít càng tốt. Cột JOIN (foreign key) nên có index.

---

## 17. Kiểm chứng bằng EXPLAIN

```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 5 AND status = 'paid';
```

- **PostgreSQL:** `Seq Scan` (quét bảng) → `Index Scan` → `Index Only Scan` (tốt nhất, covering).
- **MySQL:** cột `type`: `const` > `eq_ref` > `ref` > `range` > `index` > `ALL` (quét bảng). `Extra`: `Using index` là tốt; `Using filesort` / `Using temporary` cần tối ưu.

Quy trình: tìm truy vấn chậm → `EXPLAIN ANALYZE` → thiết kế index (equality trước range) → tạo thử → `EXPLAIN` lại → kiểm tra ảnh hưởng tới ghi → định kỳ xóa index thừa.

---

## 18. Tóm tắt

1. **Index = bản sao đã sắp xếp của cột + địa chỉ dòng gốc**, nằm trong page riêng của database. 1 index = 1 cấu trúc chứa N entry (N = số dòng).
2. **Sắp xếp** cho phép loại bỏ phần lớn dữ liệu sau mỗi lần so sánh.
3. **B-Tree** là cách tổ chức page của index thành cây: lá chứa entry thật, các tầng trên chứa mốc chỉ đường. Mỗi node = 1 page nên cây thấp và béo (1 triệu dòng ≈ 3 tầng).
4. **Mốc** = cận dưới (thường là entry đầu) của page con. Cây xây từ dưới lên: cắt theo sức chứa → lấy mốc đầu → lặp đến khi còn 1 root.
5. **Tách page** chỉ xảy ra khi page đầy: tách đôi, đẩy mốc lên cha, có thể lan lên tới root. Cây chỉ cao thêm ở đỉnh nên luôn cân bằng.
6. **Đánh đổi:** đọc nhanh hơn, ghi chậm hơn và tốn dung lượng. Luôn kiểm chứng bằng `EXPLAIN`.