[← Mục lục](./index.md)

# SSR (Server-Side Rendering)

## 1. Định nghĩa kỹ thuật

**SSR** là chiến lược render trong đó **server tạo HTML đầy đủ cho mỗi request**, gồm cả dữ liệu, rồi trả về trình duyệt. Sau đó JS tải xuống và **hydrate** để trang tương tác được.

## 2. Giải thích đơn giản

Giống **bếp nấu món mới cho từng khách**. Khách nhận món nóng, đúng yêu cầu của riêng mình, nhưng mỗi khách là một lần đứng bếp. Quán đông thì bếp quá tải và khách phải chờ lâu hơn.

## 3. Luồng hoạt động

```
Browser                       Server                      DB / API
  │ GET /cart (cookie sid)       │                           │
  │ ───────────────────────────► │ đọc cookie, xác thực      │
  │                              │ query giỏ hàng ─────────► │
  │                              │ ◄────────── dữ liệu ───── │
  │                              │ render React → HTML       │
  │ ◄── HTML đầy đủ nội dung ─── │                           │
  │ hiển thị ngay                │                           │
  │ tải JS → hydrate → tương tác │                           │
```

## 4. Đặc điểm dễ nhận biết

- **View Source** có đủ nội dung thật (tên sản phẩm, giá...).
- Dữ liệu **luôn mới** mỗi lần reload; mỗi lần reload là một lần server chạy (thấy log server).
- TTFB **cao hơn** SSG vì phải chờ server xử lý.
- Response thường có `Cache-Control: no-store` hoặc `private`.
- Next.js build output: `ƒ (Dynamic)`.
- Dùng `cookies()`, `headers()`, `searchParams` sẽ tự biến trang thành dynamic.

## 5. Ví dụ

```tsx
// app/cart/page.tsx (Next.js App Router)
import { cookies } from 'next/headers';

export const dynamic = 'force-dynamic'; // luôn render mỗi request

export default async function CartPage() {
  const sid = (await cookies()).get('sid')?.value;
  const cart = await getCart(sid); // chạy trên server
  return <h1>Giỏ hàng: {cart.items.length} món</h1>;
}
```

Với Next.js Pages Router là `getServerSideProps`.

## 6. Ưu và nhược

| Ưu | Nhược |
| --- | --- |
| SEO tốt, có nội dung ngay trong HTML | TTFB chậm, phụ thuộc tốc độ server/DB |
| Dữ liệu luôn mới, cá nhân hóa theo user | Tốn CPU server, khó scale, tốn tiền |
| FCP tốt hơn CSR | Khó cache ở CDN |
| Ẩn được logic và secret ở server | Có độ trễ giữa hiển thị và tương tác (hydration) |

## 7. Khi nào dùng

Trang **phụ thuộc request**: giỏ hàng, feed cá nhân, kết quả tìm kiếm, trang cần SEO nhưng dữ liệu đổi từng giây.

## Xem thêm

- [So sánh Rendering Strategies](./rendering-strategies.md)
- [CSR](./csr.md)
- [PPR](./ppr.md)
- [Cache](../../be/cache.md)
- [Cookie](../storage/cookie.md)
