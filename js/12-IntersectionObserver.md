# ​Intersection Observer

Intersection Observer là một API trong JavaScript cho phép bạn theo dõi sự thay đổi về khả năng hiển thị của một phần tử trong viewport hoặc một phần tử cha xác định. API này giúp thực hiện các tác vụ như lazy-loading hình ảnh, triển khai infinite scrolling và kích hoạt các hiệu ứng khi người dùng cuộn trang mà không ảnh hưởng đến hiệu suất của trang web

### **1. Tổng quan**

Intersection Observer API cho phép bạn **theo dõi khi một phần tử giao nhau (intersect) với viewport hoặc phần tử cha**. Nó cực kỳ hiệu quả cho:

- Lazy loading ảnh/nội dung
- Infinite scroll
- Animation khi scroll
- Theo dõi hiển thị cho analytics

---

### **2. Cách hoạt động**

```javascript
const observer = new IntersectionObserver(callback, options);
```

#### **Tham số:**

- **`callback`**: Hàm được gọi khi phần tử vào/ra khỏi vùng quan sát
- **`options`**: Cấu hình (optional)

---

### **3. Cấu hình Options**

```javascript
const options = {
	root: null, // Element cha để quan sát (mặc định là viewport)
	rootMargin: "0px", // Margin mở rộng vùng quan sát
	threshold: 0.5, // Ngưỡng giao nhau (0.0-1.0)
};
```

#### **Giải thích:**

- **`root`**: Phần tử container cha (nếu `null` sẽ là viewport)
- **`rootMargin`**: Tương tự CSS margin - mở rộng/kéo vào vùng quan sát
- **`threshold`**:
     - `0`: Khi bắt đầu chạm
     - `1`: Khi hoàn toàn hiển thị
     - Mảng `[0, 0.25, 0.5, 0.75, 1]` để nhận thông báo ở nhiều mức

---

### **4. Callback Function**

```javascript
const callback = (entries, observer) => {
	entries.forEach((entry) => {
		if (entry.isIntersecting) {
			// Xử lý khi element vào viewport
		} else {
			// Xử lý khi element ra khỏi viewport
		}
	});
};
```

Mỗi **`entry`** chứa:

- `target`: Element được quan sát
- `intersectionRatio`: Tỷ lệ hiển thị (0-1)
- `isIntersecting`: Boolean

---

### **5. Ví dụ thực tế**

#### **Ví dụ 1: Lazy Load Images**

```javascript
// HTML: <img data-src="real-image.jpg" class="lazy">

const lazyImages = document.querySelectorAll(".lazy");

const observer = new IntersectionObserver(
	(entries) => {
		entries.forEach((entry) => {
			if (entry.isIntersecting) {
				const img = entry.target;
				img.src = img.dataset.src;
				observer.unobserve(img); // Dừng theo dõi sau khi load
			}
		});
	},
	{
		rootMargin: "200px", // Load trước khi vào viewport 200px
	}
);

lazyImages.forEach((img) => observer.observe(img));
```

---

#### **Ví dụ 2: Infinite Scroll (React)**

```jsx
import { useEffect, useRef } from "react";

function InfiniteScroll() {
	const loaderRef = useRef(null);

	useEffect(() => {
		const observer = new IntersectionObserver(
			(entries) => {
				if (entries[0].isIntersecting) {
					// Gọi API load thêm dữ liệu
					loadMoreItems();
				}
			},
			{
				root: null,
				threshold: 0.1,
			}
		);

		if (loaderRef.current) observer.observe(loaderRef.current);

		return () => observer.disconnect();
	}, []);

	return (
		<div>
			{/* List items */}
			<div ref={loaderRef}>Loading...</div>
		</div>
	);
}
```

---

#### **Ví dụ 3: Animation khi scroll vào viewport**

```javascript
// CSS: .animate { opacity: 0; transition: opacity 0.5s; }

const animatedElements = document.querySelectorAll(".animate");

const observer = new IntersectionObserver(
	(entries) => {
		entries.forEach((entry) => {
			if (entry.isIntersecting) {
				entry.target.style.opacity = "1";
			}
		});
	},
	{
		threshold: 0.3,
	}
);

animatedElements.forEach((el) => observer.observe(el));
```

---

### **6. So sánh với Scroll Event**

| Feature              | Scroll Event      | Intersection Observer |
| -------------------- | ----------------- | --------------------- |
| Performance          | Kém (main thread) | Tốt (async)           |
| Precision            | Tự tính toán      | Tự động               |
| Complex Calculations | Cần tự xử lý      | Built-in              |
| Multi-element        | Khó quản lý       | Dễ dàng               |
| Browser Support      | All               | Modern browsers       |

---

### **7. Best Practices**

1. **Cleanup trong React**:

```jsx
useEffect(() => {
  const observer = new IntersectionObserver(...);
  return () => observer.disconnect();
}, []);
```

2. **Sử dụng `unobserve`** khi không cần thiết:

```javascript
observer.unobserve(targetElement);
```

3. **Kết hợp với CSS Containment** để tối ưu hiệu năng:

```css
.container {
	contain: layout style paint;
}
```

---

### **8. Browser Support**

- Chrome 51+
- Firefox 55+
- Safari 12.1+
- Edge 15+
- Polyfill: [intersection-observer](https://github.com/w3c/IntersectionObserver/tree/main/polyfill)

---

### **9. Giới hạn**

- Khó xử lý các case phức tạp như parallax nhiều lớp
- Không thể track real-time scroll position
- Cần polyfill cho IE11

---
