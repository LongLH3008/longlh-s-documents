[← Mục lục](./index.md)

# Rendering Strategies: CSR, SSR, SSG, ISR, PPR

## 1. Định nghĩa kỹ thuật

**Rendering strategy** là cách quyết định **HTML của một trang được tạo ở đâu và vào lúc nào**: trên trình duyệt hay server, lúc request hay lúc build, và có được cache/làm mới hay không. Lựa chọn này ảnh hưởng trực tiếp tới TTFB, FCP/LCP, SEO, độ tươi của dữ liệu và chi phí server.

| Viết tắt | Tên đầy đủ | HTML được tạo |
| --- | --- | --- |
| [CSR](./csr.md) | Client-Side Rendering | Trên **trình duyệt**, sau khi tải JS |
| [SSR](./ssr.md) | Server-Side Rendering | Trên **server**, **mỗi request** |
| [SSG](./ssg.md) | Static Site Generation | Lúc **build**, một lần |
| [ISR](./isr.md) | Incremental Static Regeneration | Lúc build, rồi **tái tạo ngầm** theo thời gian/sự kiện |
| [PPR](./ppr.md) | Partial Prerendering | **Vỏ tĩnh** lúc build + **phần động** stream lúc request |

## 2. Giải thích đơn giản

Hình dung một quán ăn:

- **CSR**: quán đưa **nguyên liệu và công thức**, khách tự nấu tại bàn. Lúc đầu bàn trống, chờ nấu xong mới có món.
- **SSR**: bếp **nấu món mới cho từng khách** khi gọi. Món nóng, đúng ý khách, nhưng bếp luôn bận.
- **SSG**: **cơm hộp làm sẵn** từ sáng. Lấy ra là có ngay, nhưng không đổi được theo khách và dần nguội.
- **ISR**: cơm hộp làm sẵn, nhưng **cứ vài phút bếp lặng lẽ làm mẻ mới** thay mẻ cũ. Khách vẫn lấy ngay, đôi khi hơi cũ một chút.
- **PPR**: **khay cơm có sẵn phần cơm, món chính** (tĩnh), còn **canh nóng** (động) được múc thêm ngay khi khách ngồi xuống.

## 3. Cách nhận biết nhanh nhất

| | CSR | SSR | SSG | ISR | PPR |
| --- | --- | --- | --- | --- | --- |
| **Dấu hiệu dễ thấy nhất** | View Source chỉ có `<div id="root"></div>` | View Source có đủ nội dung, **mỗi lần reload là một lần server chạy** | File `.html` có sẵn, mở được cả khi tắt server app | Giống SSG nhưng có **`revalidate`**, nội dung tự đổi sau một lúc | Phần vỏ hiện ngay, **skeleton** rồi mới điền dữ liệu |
| **Header/log hay gặp** | HTML rất nhỏ, không có gì đặc biệt | `Cache-Control: no-store` / `private` | `x-nextjs-cache: HIT`, `s-maxage` dài | `x-nextjs-cache: STALE` rồi `HIT` | Response **stream** (`Transfer-Encoding: chunked`) |
| **Build output Next.js** | `○ (Static)` nếu không fetch phía server | `ƒ (Dynamic)` | `○ (Static)` / `● (SSG)` | `● (SSG)` có cột Revalidate | `◐ (Partial Prerender)` |
| **Câu hỏi để phân biệt** | Dữ liệu fetch trong `useEffect`? | Có đọc cookie/header/DB mỗi request? | Trang giống hệt nhau với mọi người? | Tĩnh nhưng cần làm mới định kỳ? | Vừa có phần chung vừa có phần cá nhân? |

Mẹo: **tắt JavaScript** rồi tải trang. Trắng xóa là CSR. Có nội dung là SSR/SSG/ISR/PPR (phần động của PPR sẽ là fallback).

## 4. So sánh chi tiết

| Tiêu chí | CSR | SSR | SSG | ISR | PPR |
| --- | --- | --- | --- | --- | --- |
| Thời điểm tạo HTML | Trình duyệt | Mỗi request | Build | Build + nền | Build (vỏ) + request (động) |
| TTFB | Nhanh (HTML rỗng) | Chậm nhất (chờ server) | Nhanh nhất (CDN) | Nhanh (CDN) | Nhanh (vỏ từ CDN) |
| FCP/LCP | Chậm (chờ JS + fetch) | Tốt | Rất tốt | Rất tốt | Rất tốt |
| SEO | Kém (cần bot chạy JS) | Tốt | Tốt | Tốt | Tốt (vỏ), phần động tùy |
| Độ tươi dữ liệu | Luôn mới | Luôn mới | Cũ đến lần build sau | Cũ tối đa `revalidate` giây | Vỏ cũ, phần động luôn mới |
| Cá nhân hóa theo user | Dễ | Dễ | Không | Không | Có, ở phần động |
| Tải server | Thấp | **Cao** | Rất thấp | Thấp | Thấp đến vừa |
| Cache ở CDN | Chỉ file JS/CSS | Khó | Toàn bộ | Toàn bộ | Phần vỏ |
| Số trang lớn (hàng triệu) | Ổn | Ổn | **Build rất lâu** | Ổn (sinh dần) | Ổn |
| Ví dụ điển hình | Dashboard sau đăng nhập | Giỏ hàng, feed cá nhân | Blog, tài liệu, landing page | Trang sản phẩm, tin tức | Trang sản phẩm + giá/giỏ hàng theo user |

## 5. Chọn nhanh

```
Trang có cần SEO / hiện nhanh cho người lạ?
├─ Không (sau đăng nhập, tool nội bộ) ──────────► CSR
└─ Có
   ├─ Nội dung giống nhau với mọi người?
   │  ├─ Gần như không đổi ───────────────────────► SSG
   │  └─ Đổi theo thời gian / sự kiện ────────────► ISR
   └─ Có phần riêng theo user / request?
      ├─ Toàn trang phụ thuộc request ────────────► SSR
      └─ Chỉ một vài khối ────────────────────────► PPR
```

## 6. Ví dụ: cùng một trang, nhiều cách

Ví dụ Next.js (App Router) cho trang sản phẩm `/products/[id]`:

```tsx
// SSG: sinh sẵn lúc build
export async function generateStaticParams() {
  return (await getAllProductIds()).map((id) => ({ id }));
}

// ISR: thêm dòng này, tái tạo nền mỗi 60 giây
export const revalidate = 60;

// SSR: ép render mỗi request
export const dynamic = 'force-dynamic';
```

```tsx
// CSR: component client tự fetch
'use client';
import { useEffect, useState } from 'react';

export default function Product({ id }: { id: string }) {
  const [data, setData] = useState(null);
  useEffect(() => {
    fetch(`/api/products/${id}`).then((r) => r.json()).then(setData);
  }, [id]);
  return data ? <h1>{data.name}</h1> : <p>Đang tải...</p>;
}
```

PPR xem ví dụ ở [PPR](./ppr.md).

## 7. Lưu ý

- Các chiến lược **không loại trừ nhau trong cùng một app**. Mỗi route (thậm chí mỗi component) chọn cách riêng: trang chủ SSG, giỏ hàng SSR, dashboard CSR.
- **Hydration** áp dụng cho SSR/SSG/ISR/PPR: HTML có sẵn nhưng vẫn phải tải JS để trang tương tác được. HTML hiện sớm **không có nghĩa** là bấm được ngay.
- Tên gọi SSG/ISR/PPR gắn với hệ sinh thái Next.js nhưng ý tưởng áp dụng cho framework khác (Nuxt, Astro, SvelteKit, Remix...).

## Xem thêm

- [CSR](./csr.md), [SSR](./ssr.md), [SSG](./ssg.md), [ISR](./isr.md), [PPR](./ppr.md)
- [Cache](../../be/cache.md)
- [Edge và CDN](../../be/edge-cdn.md)
- [SSE và Streaming](../../be/sse-streaming.md)
- [REST API và Fetch](../rest-api.md)
