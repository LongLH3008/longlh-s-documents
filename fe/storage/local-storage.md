[← Mục lục](./index.md)

# localStorage

## 1. Định nghĩa kỹ thuật

**localStorage** là API thuộc Web Storage, lưu dữ liệu dạng key-value (cả key và value đều là **chuỗi**), dung lượng khoảng 5 MB cho mỗi **origin** (scheme + host + port). Dữ liệu **không có thời hạn**, tồn tại qua việc đóng tab và đóng trình duyệt cho tới khi bị xóa. API **đồng bộ (synchronous)** và **không tự gửi** lên server.

## 2. Giải thích đơn giản

Giống **tủ cá nhân trong nhà**: đồ để đó mãi, mọi phòng (mọi tab cùng site) đều mở được. Nhưng ai lọt vào nhà, tức là script độc hại chạy trong trang, cũng mở được tủ. Muốn đưa đồ cho người ngoài (server) thì phải tự mang ra.

## 3. Đặc điểm dễ nhận biết

- Mở **tab mới** cùng site thì dữ liệu **vẫn còn**; tắt trình duyệt mở lại **vẫn còn**.
- Không xuất hiện trong header request (khác cookie).
- Xem trong DevTools → Application → **Local Storage**.
- Các tab cùng origin nhận được sự kiện `storage` khi giá trị thay đổi ở tab khác.

## 4. Ví dụ

```js
localStorage.setItem('theme', 'dark');
localStorage.getItem('theme');   // 'dark'
localStorage.removeItem('theme');
localStorage.clear();            // xóa toàn bộ của origin này

// Lưu object phải tự serialize
localStorage.setItem('user', JSON.stringify({ id: 1, name: 'An' }));
const user = JSON.parse(localStorage.getItem('user') ?? 'null');

// Đồng bộ trạng thái giữa các tab
window.addEventListener('storage', (e) => {
  if (e.key === 'theme') applyTheme(e.newValue);
});
```

## 5. Khi nào dùng và không dùng

| Nên dùng | Không nên dùng |
| --- | --- |
| Theme, ngôn ngữ, cấu hình giao diện | **Access/refresh token, session id** (XSS đọc được) |
| Cache nhỏ phía client, dữ liệu nháp | Dữ liệu cần server đọc mỗi request |
| Cờ "đã xem hướng dẫn" | Dữ liệu lớn hoặc nhiều (dùng IndexedDB) |

## 6. Lưu ý

- **Đồng bộ nên chặn main thread** nếu ghi dữ liệu lớn.
- Không có thời gian hết hạn tự động, phải tự quản lý (ví dụ lưu kèm timestamp).
- Có thể bị lỗi hoặc không dùng được ở chế độ riêng tư hoặc khi đầy quota; nên bọc `try/catch`.
- Không có ở môi trường server (SSR sẽ lỗi `localStorage is not defined`), chỉ truy cập trong `useEffect` hoặc sau khi kiểm tra `typeof window !== 'undefined'`, xem [CSR](../rendering/csr.md) và [SSR](../rendering/ssr.md).

## Xem thêm

- [So sánh Browser Storage và Credentials](./browser-storage.md)
- [Cookie](./cookie.md)
- [sessionStorage](./session-storage.md)
- [XSS](../../security/xss.md)
