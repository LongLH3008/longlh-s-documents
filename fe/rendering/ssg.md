[← Mục lục](./index.md)

# SSG (Static Site Generation)

## 1. Định nghĩa kỹ thuật

**SSG** là chiến lược render trong đó **HTML được tạo sẵn một lần lúc build**, lưu thành file tĩnh và phục vụ qua CDN. Mọi người dùng nhận **cùng một file**, server không phải chạy gì lúc request.

## 2. Giải thích đơn giản

Giống **in sẵn một chồng tờ rơi**. Ai đến cũng lấy ngay một tờ, rất nhanh và rẻ. Nhưng muốn sửa nội dung thì phải **in lại cả chồng** (build lại), và tờ rơi không thể ghi tên riêng từng người.

## 3. Luồng hoạt động

```
Lúc BUILD:
  CI chạy build ──► lấy dữ liệu (CMS/DB) ──► sinh product-1.html, product-2.html, ...
                                              └──► đẩy lên CDN

Lúc REQUEST:
  Browser ──► CDN ──► trả file .html có sẵn (không qua server app)
```

## 4. Đặc điểm dễ nhận biết

- Có **file `.html` thật** sau khi build (thư mục `out/`, `dist/`, `_site/`).
- TTFB **nhanh nhất**, trang vẫn chạy khi tắt backend.
- Nội dung **không đổi** cho tới lần build kế tiếp.
- Next.js build output: `○ (Static)` hoặc `● (SSG)` (có `generateStaticParams`).
- Thường gắn với blog, tài liệu, landing page.

## 5. Ví dụ

```tsx
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await getAllPosts();
  return posts.map((p) => ({ slug: p.slug })); // build ra 1 trang cho mỗi slug
}

export default async function Post({ params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params;
  const post = await getPost(slug);
  return <article><h1>{post.title}</h1></article>;
}
```

Với Next.js Pages Router là `getStaticProps` + `getStaticPaths`. Các công cụ SSG khác: Astro, Hugo, Jekyll, Eleventy, Docusaurus. Chính kho ghi chú này được build bằng Jekyll (xem `_config.yml`).

## 6. Ưu và nhược

| Ưu | Nhược |
| --- | --- |
| Nhanh nhất, cache toàn bộ ở CDN | Dữ liệu cũ đến lần build sau |
| Rẻ, chịu tải cực tốt, ít bề mặt tấn công (không có server render) | Không cá nhân hóa trong HTML |
| SEO rất tốt | Hàng trăm nghìn trang thì **build rất lâu** |

## 7. Khi nào dùng

Nội dung **ít đổi, giống nhau với mọi người**: blog, docs, trang giới thiệu, marketing. Nếu cần làm mới định kỳ mà không build lại toàn bộ, xem [ISR](./isr.md).

## Xem thêm

- [So sánh Rendering Strategies](./rendering-strategies.md)
- [ISR](./isr.md)
- [Edge và CDN](../../be/edge-cdn.md)
- [Cache](../../be/cache.md)
