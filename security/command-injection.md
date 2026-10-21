# Command Injection và Path Traversal

## 1. Định nghĩa kỹ thuật

**Command Injection** là lỗ hổng khi ứng dụng **ghép input người dùng vào lệnh shell của hệ điều hành** rồi thực thi. Kẻ tấn công chèn thêm ký tự điều khiển shell (`;`, `&&`, `|`, `` ` ``, `$()`) để chạy lệnh tuỳ ý với quyền của tiến trình server.

**Path Traversal** (directory traversal) là lỗ hổng khi input được dùng để dựng **đường dẫn file** mà không kiểm tra, cho phép dùng `../` thoát khỏi thư mục dự định và đọc/ghi file tuỳ ý.

Cả hai cùng bản chất với [SQL Injection](./sql-injection.md): dữ liệu bị diễn giải thành mã/đường dẫn điều khiển.

## 2. Giải thích đơn giản

- **Command Injection**: bạn nhờ thư ký "gọi điện cho `<tên>`". Kẻ xấu điền tên là "Lan, rồi xoá hết hồ sơ". Nếu thư ký đọc nguyên văn như một chuỗi chỉ thị, họ làm cả phần sau.
- **Path Traversal**: bạn chỉ được lấy tài liệu trong ngăn tủ A. Kẻ xấu xin "ngăn A, lùi ra ngoài hai cấp, rồi lấy két sắt". Nếu không ai kiểm tra, họ đi ra khỏi tủ A.

## 3. Ví dụ

### 3.1 Command Injection

```js
// Dễ bị tấn công
import { exec } from 'node:child_process';
exec(`ping -c 1 ${req.query.host}`, cb);
// host = "8.8.8.8; cat /etc/passwd"  → chạy thêm lệnh thứ hai
```

### 3.2 Path Traversal

```js
// Dễ bị tấn công
app.get('/download', (req, res) => {
  res.sendFile(`/var/app/uploads/${req.query.file}`);
});
// file = "../../../etc/passwd"
```

Biến thể: `..%2f` (URL-encode), `%252e%252e%252f` (double encode), `..\` trên Windows, byte null.

## 4. Cách phòng chống

### 4.1 Command Injection

1. **Tránh gọi shell** nếu có thư viện/API tương đương (dùng thư viện DNS thay vì `ping`).
2. Nếu bắt buộc, dùng API truyền **mảng tham số**, không qua shell:

```js
import { execFile } from 'node:child_process';
execFile('ping', ['-c', '1', host], cb); // host là một đối số, không bị diễn giải
```

3. **Allowlist/validate** input (regex chặt, kiểm tra là IP/hostname hợp lệ), đồng thời chặn giá trị bắt đầu bằng `-` (tránh bị hiểu là option).
4. Chạy tiến trình với **quyền tối thiểu**, trong container/sandbox.

### 4.2 Path Traversal

1. Không dùng tên file người dùng nhập trực tiếp; lưu file với **ID ngẫu nhiên** và tra tên trong DB. Xem [Upload File Lớn](../be/upload-heavy-file.md).
2. Nếu phải dùng đường dẫn: **chuẩn hoá rồi kiểm tra** nằm trong thư mục gốc cho phép.

```js
import path from 'node:path';

const BASE = path.resolve('/var/app/uploads');

function safePath(name) {
  const full = path.resolve(BASE, name);
  if (!full.startsWith(BASE + path.sep)) throw new Error('forbidden');
  return full;
}
```

3. Allowlist phần mở rộng file, và trả đúng `Content-Type` kèm `nosniff` ([Security Headers](./security-headers.md)).
4. Quyền hệ thống file: tiến trình web chỉ đọc được thư mục cần thiết.

## 5. Lưu ý

- Chạy tiến trình nền hay [Background Worker](../be/background-worker.md) xử lý file/lệnh từ input người dùng cũng phải áp dụng các quy tắc này.
- Thư viện xử lý ảnh/video/PDF bên thứ ba cũng hay gọi shell phía sau, cần cập nhật và cô lập.
- Cùng họ với lỗi injection khác: SQL, NoSQL, LDAP, template (SSTI), header. Nguyên tắc chung: **tách mã khỏi dữ liệu**.
