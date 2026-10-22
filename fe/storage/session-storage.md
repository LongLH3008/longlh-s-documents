[← Mục lục](./index.md)

# sessionStorage

## 1. Định nghĩa kỹ thuật

**sessionStorage** là API thuộc Web Storage, có giao diện giống [localStorage](./local-storage.md) nhưng dữ liệu gắn với **một browsing context (một tab)** cho từng origin. Dữ liệu tồn tại khi **reload** trang và bị xóa khi **đóng tab**. Dung lượng khoảng 5 MB, đồng bộ, không tự gửi lên server.

## 2. Giải thích đơn giản

Giống **tờ giấy nhớ dán trên bàn của một tab**. Tab khác có tờ giấy riêng, không thấy tờ của bạn. Đóng tab là tờ giấy bị vứt. Rất hợp để ghi tạm những thứ chỉ cần trong lúc đang làm việc ở tab đó.

Lưu ý: tên "session" ở đây **không liên quan** tới session đăng nhập ở server.

## 3. Đặc điểm dễ nhận biết

- Mở **tab mới** cùng site thì **không thấy** dữ liệu.
- **Reload** tab thì dữ liệu **còn**.
- **Đóng tab** thì mất.
- "Duplicate tab" sẽ **sao chép** giá trị hiện tại sang tab mới, sau đó hai bên độc lập.
- DevTools → Application → **Session Storage**.

## 4. Ví dụ

```js
sessionStorage.setItem('checkoutStep', '2');
sessionStorage.getItem('checkoutStep'); // '2'
sessionStorage.removeItem('checkoutStep');

// Lưu nháp form để không mất khi lỡ reload
const form = document.querySelector('form');
form.addEventListener('input', () => {
  sessionStorage.setItem('draft', JSON.stringify(Object.fromEntries(new FormData(form))));
});
```

## 5. Khi nào dùng và không dùng

| Nên dùng | Không nên dùng |
| --- | --- |
| Bước hiện tại của wizard/checkout | Token hoặc secret (XSS vẫn đọc được) |
| Nháp form trong một lần làm việc | Dữ liệu cần dùng ở nhiều tab hoặc lần mở sau |
| Trạng thái scroll/filter riêng từng tab | Dữ liệu cần server đọc |

## 6. Lưu ý

- Cũng **đọc được bằng JS** nên không chống được [XSS](../../security/xss.md).
- Mở link bằng `target="_blank"` có thể sao chép sessionStorage sang tab mới tại thời điểm mở (hành vi tuỳ trình duyệt), đừng giả định tuyệt đối là "tab nào ra tab đó".
- Không có ở môi trường server, truy cập sau khi chắc chắn đang chạy ở trình duyệt.

## Xem thêm

- [So sánh Browser Storage và Credentials](./browser-storage.md)
- [Cookie](./cookie.md)
- [localStorage](./local-storage.md)
