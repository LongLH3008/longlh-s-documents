# SQL Injection

## 1. Định nghĩa kỹ thuật

**SQL Injection (SQLi)** là lỗ hổng xảy ra khi ứng dụng **ghép trực tiếp dữ liệu người dùng vào câu lệnh SQL** dưới dạng chuỗi. Kẻ tấn công chèn cú pháp SQL vào input để **thay đổi cấu trúc câu truy vấn**: đọc dữ liệu trái phép, bỏ qua đăng nhập, sửa/xoá dữ liệu, thậm chí thực thi lệnh trên máy chủ DB.

Gốc rễ giống [XSS](./xss.md): **dữ liệu bị diễn giải thành mã**, ở đây là mã SQL.

## 2. Giải thích đơn giản

Hãy tưởng tượng một mẫu giấy "Tìm nhân viên tên: ______". Bạn điền `Lan` thì được tìm Lan. Nhưng nếu kẻ xấu điền `Lan hoặc tất cả mọi người`, và người xử lý đọc nguyên văn như một lệnh, họ sẽ trả về toàn bộ nhân viên. Dữ liệu điền vào đã **biến thành một phần của lệnh**.

## 3. Ví dụ

### 3.1 Code dễ bị tấn công

```js
const sql = `SELECT * FROM users WHERE email = '${email}' AND password = '${password}'`;
```

Kẻ tấn công nhập email là `' OR '1'='1' --`:

```sql
SELECT * FROM users WHERE email = '' OR '1'='1' --' AND password = ''
```

Điều kiện luôn đúng, phần sau bị comment, nên đăng nhập được mà không cần mật khẩu.

### 3.2 Các loại

| Loại | Mô tả |
| --- | --- |
| **In-band (Union / Error-based)** | Kết quả trả về trực tiếp trong response, ví dụ `UNION SELECT` để lấy bảng khác |
| **Blind (Boolean / Time-based)** | Không thấy dữ liệu, suy ra từng bit qua đúng/sai hoặc độ trễ (`SLEEP(5)`) |
| **Out-of-band** | Dữ liệu thoát qua kênh khác (DNS, HTTP) |
| **Second-order** | Payload được lưu an toàn, nhưng bị dùng lại để ghép vào query ở chỗ khác |

## 4. Cách phòng chống

### 4.1 Parameterized query / Prepared statement (bắt buộc)

Tách **câu lệnh** và **tham số**. DB biên dịch cấu trúc trước, tham số chỉ là giá trị, không bao giờ được hiểu là SQL.

```js
// node-postgres
const { rows } = await db.query(
  'SELECT * FROM users WHERE email = $1 AND password_hash = $2',
  [email, hash],
);
```

```js
// mysql2
await conn.execute('SELECT * FROM users WHERE id = ?', [id]);
```

### 4.2 ORM / Query builder

ORM (Prisma, TypeORM, Sequelize, Knex) tham số hoá mặc định. Cẩn thận với API "raw":

```js
// Nguy hiểm
sequelize.query(`SELECT * FROM users WHERE name = '${name}'`);
// An toàn
sequelize.query('SELECT * FROM users WHERE name = :name', { replacements: { name } });
```

### 4.3 Phần không tham số hoá được

Tên bảng, tên cột, `ORDER BY`, hướng sắp xếp **không** truyền được bằng tham số. Hãy dùng **allowlist**:

```js
const sortable = new Set(['created_at', 'price']);
const col = sortable.has(req.query.sort) ? req.query.sort : 'created_at';
const sql = `SELECT * FROM products ORDER BY ${col}`;
```

Lưu ý tương tự khi làm [phân trang](../db/offset-pagination.md): `limit`, `offset` phải ép về số nguyên.

### 4.4 Least privilege

Tài khoản DB của ứng dụng chỉ có quyền cần thiết (không phải superuser, không `DROP`, không đọc bảng nhạy cảm). Nếu bị tấn công, thiệt hại bị giới hạn.

### 4.5 Khác

- Validate kiểu và độ dài input (lớp bổ trợ, không thay cho tham số hoá).
- Không trả lỗi SQL chi tiết ra client; log nội bộ.
- Băm mật khẩu bằng bcrypt/argon2, không lưu plaintext.
- Giới hạn tần suất để làm chậm dò blind SQLi: [Rate Limit](../be/rate-limit.md).
- Quét bằng công cụ SAST/DAST, WAF làm lớp phụ.

## 5. Lưu ý

- **Escape thủ công** (`replace("'", "''")`) rất dễ sót, đừng dùng thay cho prepared statement.
- NoSQL cũng có injection tương tự (ví dụ truyền object `{"$ne": null}` vào query MongoDB). Luôn kiểm tra kiểu dữ liệu đầu vào.
- Chỉ mục và truy vấn hiệu quả xem ở [Index B-Tree](../db/index-btree.md); bảo mật và hiệu năng không loại trừ nhau, prepared statement còn giúp cache plan.
