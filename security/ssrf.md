[← Mục lục](./index.md)

# SSRF (Server-Side Request Forgery)

## 1. Định nghĩa kỹ thuật

**SSRF** là lỗ hổng cho phép kẻ tấn công **khiến server gửi request HTTP (hoặc giao thức khác) tới địa chỉ do kẻ tấn công chọn**. Vì request xuất phát từ **bên trong hạ tầng** của server, nó có thể chạm tới những tài nguyên mà bên ngoài không truy cập được: dịch vụ nội bộ, database admin, cloud metadata (`169.254.169.254`), localhost.

Thường xuất hiện ở chức năng: tải ảnh từ URL, webhook, import từ link, preview link, render PDF từ URL.

## 2. Giải thích đơn giản

Bạn nhờ một nhân viên lễ tân: "Mang giúp tôi tờ giấy ở địa chỉ này". Nhân viên được phép vào cả khu vực nội bộ. Kẻ xấu đưa một địa chỉ **trong phòng kho tài liệu mật**, và nhân viên tin tưởng đi lấy về giùm. Kẻ xấu không vào được kho nhưng **mượn quyền của nhân viên**.

## 3. Ví dụ

```js
// Dễ bị tấn công: tải ảnh từ URL người dùng cung cấp
app.get('/fetch-image', async (req, res) => {
  const r = await fetch(req.query.url);
  res.send(Buffer.from(await r.arrayBuffer()));
});
```

Kẻ tấn công gọi:

```
/fetch-image?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/
/fetch-image?url=http://localhost:6379/            # Redis nội bộ
/fetch-image?url=http://10.0.0.5:8080/admin
```

Server trả về credential của cloud hoặc nội dung dịch vụ nội bộ cho kẻ tấn công.

### Các loại

- **Basic SSRF**: response được trả về cho kẻ tấn công.
- **Blind SSRF**: không thấy response, nhưng quét cổng nội bộ qua thời gian hoặc kênh phụ (DNS callback).

## 4. Các kỹ thuật vượt bộ lọc phổ biến

| Kỹ thuật | Ví dụ |
| --- | --- |
| Biểu diễn IP khác | `http://2130706433/`, `http://0x7f.1/`, `http://[::1]/` |
| DNS trỏ về IP nội bộ | `evil.test` phân giải thành `127.0.0.1` |
| **DNS rebinding** | Lần kiểm tra trả IP public, lần fetch thật trả IP nội bộ |
| Redirect | URL public trả `302` tới địa chỉ nội bộ |
| Scheme khác | `file://`, `gopher://`, `ftp://` |
| `@` trong URL | `http://allowed.test@evil.test/` |

## 5. Cách phòng chống

### 5.1 Allowlist đích đến

Nếu chỉ cần gọi vài dịch vụ biết trước, chỉ cho phép đúng domain đó. Đây là cách chắc nhất.

### 5.2 Nếu phải nhận URL tuỳ ý

1. Chỉ cho phép scheme `http`/`https`.
2. **Phân giải DNS trước**, kiểm tra IP kết quả không thuộc dải private/loopback/link-local (`127.0.0.0/8`, `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `169.254.0.0/16`, `::1`, `fc00::/7`).
3. **Kết nối bằng chính IP đã kiểm tra** (pin IP) để chống DNS rebinding.
4. **Tắt tự động follow redirect**, hoặc kiểm tra lại từng bước redirect.
5. Đặt timeout và giới hạn kích thước response.

```js
import dns from 'node:dns/promises';
import net from 'node:net';

async function assertPublic(urlStr) {
  const { protocol, hostname } = new URL(urlStr);
  if (!['http:', 'https:'].includes(protocol)) throw new Error('scheme');
  const { address } = await dns.lookup(hostname);
  if (isPrivate(address)) throw new Error('private ip'); // tự cài isPrivate
  return address;
}
```

### 5.3 Phòng thủ ở hạ tầng

- Chạy dịch vụ fetch trong mạng **tách biệt**, firewall chặn truy cập tới dải nội bộ.
- Cloud: bật **IMDSv2** (yêu cầu token) và hạn chế quyền IAM của instance.
- Dịch vụ nội bộ cũng cần xác thực, không dựa vào "chỉ truy cập được từ trong mạng".
- Không trả nguyên response thô cho người dùng; chỉ trả dữ liệu đã xử lý (ví dụ ảnh đã được kiểm tra MIME).

## 6. Liên hệ

- Hành vi tải file từ URL thường đi cùng [Upload File Lớn](../be/upload-heavy-file.md); áp dụng các kiểm tra trên cho bước fetch.
- Giới hạn tần suất gọi chức năng này bằng [Rate Limit](../be/rate-limit.md).
- Nhầm lẫn thường gặp với [CSRF](./csrf.md): CSRF lợi dụng **trình duyệt nạn nhân**, SSRF lợi dụng **server**.
