[← Mục lục](./index.md)

# ISR (Incremental Static Regeneration)

## 1. Định nghĩa kỹ thuật

**ISR** là mở rộng của [SSG](./ssg.md): trang vẫn được phục vụ như file tĩnh từ cache, nhưng **được tái tạo (regenerate) ngầm** trên server sau một khoảng thời gian (`revalidate`) hoặc khi có sự kiện (on-demand). Trang chưa từng build có thể được **sinh ở request đầu tiên** rồi cache lại. Cơ chế dựa trên mô hình **stale-while-revalidate**.

## 2. Giải thích đơn giản

Giống **cửa hàng tiện lợi có kệ hàng dựng sẵn**. Khách lấy hàng trên kệ ngay, không phải chờ. Cứ vài phút nhân viên **lặng lẽ thay mẻ mới** vào kệ. Người đến đúng lúc kệ sắp hết hạn vẫn nhận hàng cũ (hơi cũ chút), người đến sau sẽ nhận hàng mới.

## 3. Luồng hoạt động (time-based)

```
revalidate = 60 giây

t=0     build ra trang v1
t=30    request ──► trả v1 (HIT, còn tươi)
t=70    request ──► trả v1 (STALE) ──► kích hoạt tái tạo ngầm ──► v2 được lưu
t=75    request ──► trả v2 (HIT)
```

Người dùng **không bao giờ phải chờ** tái tạo. Chỉ request đầu tiên sau khi hết hạn nhận bản cũ.

## 4. Đặc điểm dễ nhận biết

- Có cấu hình **`revalidate`** (giây) hoặc gọi `revalidatePath` / `revalidateTag`.
- Header `x-nextjs-cache`: `MISS` → `HIT` → `STALE` (sau khi hết hạn) → `HIT`.
- Reload liên tục thì nội dung **đổi chậm** (không đổi mỗi lần như SSR, không bao giờ đổi như SSG).
- Next.js build output: `● (SSG)` kèm cột **Revalidate**.
- Có thể thêm trang mới **mà không cần build lại** toàn site.

## 5. Ví dụ

```tsx
// 1. Time-based
export const revalidate = 60; // tối đa 60 giây thì làm mới

export default async function Product({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params;
  const product = await getProduct(id);
  return <h1>{product.name} - {product.price}</h1>;
}
```

```ts
// 2. On-demand: gọi khi admin cập nhật sản phẩm (Route Handler / Server Action)
import { revalidatePath, revalidateTag } from 'next/cache';

revalidatePath('/products/42'); // làm mới đúng 1 trang
revalidateTag('products');      // làm mới mọi trang gắn tag này
```

## 6. Ưu và nhược

| Ưu | Nhược |
| --- | --- |
| Tốc độ như SSG, nội dung gần như luôn mới | Vẫn có thể trả **bản cũ** trong cửa sổ `revalidate` |
| Không cần build lại toàn bộ khi sửa nội dung | Cần runtime server/CDN hỗ trợ (không chỉ file tĩnh thuần) |
| Scale tốt với số trang lớn (sinh dần) | Cá nhân hóa theo user vẫn **không** làm được |
| On-demand revalidate cho cập nhật tức thì | Cache nhiều tầng (CDN + server) khó debug |

## 7. Khi nào dùng

Nội dung **đổi thỉnh thoảng nhưng không cần real-time**: trang sản phẩm, bài báo, danh sách tin, trang giá.

## Xem thêm

- [So sánh Rendering Strategies](./rendering-strategies.md)
- [SSG](./ssg.md)
- [PPR](./ppr.md)
- [Cache](../../be/cache.md)
- [Edge và CDN](../../be/edge-cdn.md)
