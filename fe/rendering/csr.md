[← Mục lục](./index.md)

# CSR (Client-Side Rendering)

## 1. Định nghĩa kỹ thuật

**CSR** là chiến lược render trong đó server trả về một **HTML gần như rỗng** kèm bundle JavaScript. Trình duyệt tải JS, chạy, **gọi API lấy dữ liệu** rồi tự dựng DOM. Đây là mô hình mặc định của SPA (React + Vite, Vue, Angular thuần).

## 2. Giải thích đơn giản

Server đưa cho bạn **hộp đồ chơi lắp ghép chưa lắp**. Trình duyệt phải tự lắp. Lúc đầu thấy màn hình trắng hoặc spinner, lắp xong mới thấy hình. Đổi lại, sau khi lắp xong thì chuyển trang rất mượt vì không phải xin hộp mới.

## 3. Luồng hoạt động

```
Browser                         Server / CDN                 API
  │ GET /dashboard                  │                          │
  │ ──────────────────────────────► │                          │
  │ ◄── <div id="root"></div> + JS ─│                          │
  │ tải JS, chạy React              │                          │
  │ GET /api/me, /api/stats ───────────────────────────────►   │
  │ ◄──────────────────────────────────────── JSON ─────────── │
  │ dựng DOM, hiển thị                                         │
```

## 4. Đặc điểm dễ nhận biết

- **View Source** chỉ thấy `<div id="root"></div>` và thẻ `<script>`.
- Tắt JavaScript là **trắng trang**.
- Có giai đoạn **spinner / skeleton** sau khi trang mở.
- Tab Network: HTML nhỏ, sau đó mới có request `fetch`/`XHR` lấy dữ liệu (waterfall: HTML → JS → API).

## 5. Ví dụ

```html
<!-- Toàn bộ HTML server trả về -->
<body>
  <div id="root"></div>
  <script type="module" src="/assets/index-a1b2c3.js"></script>
</body>
```

```jsx
import { useEffect, useState } from 'react';

function Dashboard() {
  const [stats, setStats] = useState(null);
  useEffect(() => {
    fetch('/api/stats').then((r) => r.json()).then(setStats);
  }, []);

  if (!stats) return <p>Đang tải...</p>;
  return <h1>Doanh thu: {stats.revenue}</h1>;
}
```

## 6. Ưu và nhược

| Ưu | Nhược |
| --- | --- |
| Hosting đơn giản, chỉ cần file tĩnh | FCP/LCP chậm do chờ JS rồi mới fetch |
| Chuyển trang sau lần đầu rất mượt | SEO và preview link (OG tags) kém |
| Server không tốn CPU render | Bundle lớn thì máy yếu chạy chậm |
| Cá nhân hóa dễ | Waterfall request (JS → API → dữ liệu phụ) |

## 7. Khi nào dùng

Dashboard, trang quản trị, app sau đăng nhập, tool nội bộ: nơi **không cần SEO** và người dùng ở lại lâu.

## Xem thêm

- [So sánh Rendering Strategies](./rendering-strategies.md)
- [SSR](./ssr.md)
- [REST API và Fetch](../rest-api.md)
- [AbortController](../abort-controller.md)
