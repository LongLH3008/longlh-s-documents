[← Mục lục](./index.md)

# PPR (Partial Prerendering)

## 1. Định nghĩa kỹ thuật

**PPR** là chiến lược render lai trong **một route**: phần **tĩnh** (static shell) được prerender lúc build và phục vụ ngay từ CDN, còn các phần **động** nằm trong ranh giới `<Suspense>` được **render lúc request rồi stream** vào cùng một response HTTP. Khái niệm do Next.js đưa ra (dựa trên React Suspense và streaming), ban đầu là tính năng thử nghiệm ở Next.js 15.

> Lưu ý: PPR thay đổi nhanh theo phiên bản. Ở các bản Next.js mới hơn, cờ `experimental.ppr` được thay dần bằng mô hình **Cache Components** (`cacheComponents`). Ý tưởng "vỏ tĩnh + phần động stream" vẫn giữ nguyên. Hãy kiểm tra tài liệu của đúng phiên bản đang dùng trước khi cấu hình.

## 2. Giải thích đơn giản

Giống **khay cơm có sẵn cơm và món chính** (chuẩn bị từ sáng, đưa ngay), còn **canh nóng** được múc thêm khi khách ngồi xuống. Khách thấy khay ngay, canh đến sau vài giây. Không phải chờ cả bữa chỉ vì một món cần nóng.

## 3. Luồng hoạt động

```
Lúc BUILD:  prerender vỏ tĩnh (header, tên sản phẩm, mô tả) + fallback cho chỗ động

Lúc REQUEST (một HTTP response duy nhất, dạng stream):
  Browser ──► CDN/Edge ──► gửi NGAY vỏ tĩnh + skeleton
                     └──► server render phần động (giá theo user, giỏ hàng)
                            └──► stream thêm HTML thay vào chỗ skeleton
```

## 4. Đặc điểm dễ nhận biết

- Trang hiện **vỏ ngay lập tức**, các khối cá nhân hóa hiện **skeleton rồi mới điền dữ liệu**.
- Trong code có `<Suspense fallback={...}>` bao quanh component dùng `cookies()`, `headers()` hoặc fetch không cache.
- Response ở dạng **chunked/streaming**.
- Next.js build output: `◐ (Partial Prerender)`.
- Khác SSR: không phải chờ server xong mới gửi byte đầu tiên. Khác SSG/ISR: có phần cá nhân hóa theo request.

## 5. Ví dụ

```ts
// next.config.ts (Next.js 15)
const nextConfig = { experimental: { ppr: 'incremental' } };
export default nextConfig;
```

```tsx
// app/products/[id]/page.tsx
import { Suspense } from 'react';
import { cookies } from 'next/headers';

export const experimental_ppr = true; // bật PPR cho route này

export default async function ProductPage({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params;
  const product = await getProduct(id); // tĩnh: nằm trong vỏ

  return (
    <main>
      <h1>{product.name}</h1>
      <p>{product.description}</p>

      {/* Phần động: render lúc request, stream vào sau */}
      <Suspense fallback={<p>Đang tải giá của bạn...</p>}>
        <PersonalPrice id={id} />
      </Suspense>
    </main>
  );
}

async function PersonalPrice({ id }: { id: string }) {
  const sid = (await cookies()).get('sid')?.value;
  const price = await getPriceForUser(id, sid);
  return <strong>{price}</strong>;
}
```

## 6. Ưu và nhược

| Ưu | Nhược |
| --- | --- |
| FCP/LCP như SSG, vẫn cá nhân hóa như SSR | Còn mới, API và cờ cấu hình thay đổi theo phiên bản |
| Không phải chọn "tĩnh hay động" cho cả trang | Gắn chặt với framework hỗ trợ (hiện chủ yếu Next.js) |
| Một request, một response | Phải thiết kế ranh giới `Suspense` cẩn thận để tránh layout shift |

## 7. Khi nào dùng

Trang **vừa có phần chung vừa có phần riêng**: trang sản phẩm (mô tả tĩnh + giá/tồn kho/giỏ hàng theo user), trang chủ có khối "dành cho bạn".

## Xem thêm

- [So sánh Rendering Strategies](./rendering-strategies.md)
- [SSR](./ssr.md)
- [SSG](./ssg.md)
- [SSE và Streaming](../../be/sse-streaming.md)
- [Cookie](../storage/cookie.md)
