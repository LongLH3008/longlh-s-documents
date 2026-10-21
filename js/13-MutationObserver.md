# MutationObserver

### **1. Giới thiệu**

**MutationObserver** là một Web API cho phép theo dõi các thay đổi trong cấu trúc DOM, bao gồm:

- Thêm/xóa phần tử con
- Thay đổi thuộc tính (attributes)
- Thay đổi nội dung văn bản
- Thay đổi các node trong cây DOM

---

### **2. Cách hoạt động**

```javascript
const observer = new MutationObserver(callback);
```

#### **Phương thức chính:**

- **`observe(target, options)`**: Bắt đầu theo dõi phần tử
- **`disconnect()`**: Dừng theo dõi
- **`takeRecords()`**: Lấy danh sách thay đổi chưa xử lý

---

### **3. Cấu hình Options**

```javascript
const options = {
	attributes: true, // Theo dõi thay đổi thuộc tính
	childList: true, // Theo dõi thêm/xóa phần tử con
	subtree: true, // Theo dõi cả cây con
	attributeFilter: ["class", "id"], // Chỉ theo dõi các thuộc tính cụ thể
	attributeOldValue: true, // Lưu giá trị thuộc tính cũ
	characterData: true, // Theo dõi thay đổi nội dung văn bản
};
```

---

### **4. Callback Function**

```javascript
const callback = (mutationsList, observer) => {
	for (const mutation of mutationsList) {
		switch (mutation.type) {
			case "childList":
				// Xử lý thay đổi phần tử con
				break;
			case "attributes":
				// Xử lý thay đổi thuộc tính
				console.log(`Thuộc tính ${mutation.attributeName} thay đổi`);
				break;
			case "characterData":
				// Xử lý thay đổi nội dung
				break;
		}
	}
};
```

---

### **5. Ví dụ thực tế**

#### **Ví dụ 1: Theo dõi thay đổi thuộc tính**

```javascript
// HTML: <div id="target" class="box"></div>

const target = document.getElementById("target");
const observer = new MutationObserver((mutations) => {
	mutations.forEach((mutation) => {
		if (mutation.type === "attributes" && mutation.attributeName === "class") {
			console.log("Class mới:", target.className);
		}
	});
});

observer.observe(target, {
	attributes: true,
	attributeFilter: ["class"], // Chỉ theo dõi class
});

// Thay đổi class sẽ kích hoạt callback
target.classList.add("active");
```

---

#### **Ví dụ 2: Phát hiện phần tử bị xóa**

```javascript
const parent = document.querySelector(".list");
const observer = new MutationObserver((mutations) => {
	mutations.forEach((mutation) => {
		mutation.removedNodes.forEach((node) => {
			console.log("Phần tử bị xóa:", node);
		});
	});
});

observer.observe(parent, {
	childList: true,
});

// Xóa phần tử con
parent.removeChild(parent.firstElementChild);
```

---

#### **Ví dụ 3: Theo dõi toàn bộ cây DOM (React/Vue)**

```javascript
// Theo dõi mọi thay đổi trong body
const observer = new MutationObserver((mutations) => {
	console.log("DOM đã thay đổi:", mutations);
});

observer.observe(document.body, {
	childList: true,
	subtree: true,
	attributes: true,
});
```

---

### **6. Ứng dụng thực tế**

1. **Chống XSS**: Phát hiện và chặn các script độc được chèn vào DOM
2. **Undo/Redo**: Ghi lại lịch sử thay đổi DOM
3. **Form Validation**: Tự động validate khi nội dung thay đổi
4. **Framework Internals**: React/Vue sử dụng để theo dõi cập nhật DOM
5. **Dynamic Content**: Xử lý khi nội dung được tải động

---

### **7. So sánh với các phương pháp cũ**

| Phương pháp      | Mutation Events | MutationObserver |
| ---------------- | --------------- | ---------------- |
| Hiệu năng        | Rất kém         | Tốt              |
| Cơ chế           | Sync            | Async            |
| Browser Support  | Lỗi thời        | Modern browsers  |
| Control          | Không           | Chi tiết         |
| Memory Leak Risk | Cao             | Thấp             |

---

### **8. Best Practices**

1. **Luôn disconnect khi không dùng**:

```javascript
// React useEffect cleanup
useEffect(() => {
  const observer = new MutationObserver(...);
  return () => observer.disconnect();
}, []);
```

2. **Hạn chế theo dõi phạm vi rộng**:

```javascript
// Tệ: observer.observe(document.body, { subtree: true })
// Tốt: observer.observe(specificElement, { childList: true })
```

3. **Sử dụng `attributeFilter`** để giảm số lần callback:

```javascript
{ attributes: true, attributeFilter: ['data-custom'] }
```

---

### **9. Giới hạn**

- Không theo dõi được CSS changes (dùng `ResizeObserver` cho kích thước)
- Không phát hiện thay đổi từ `document.write`
- Callback không chạy ngay lập tức khi có thay đổi

---

### **10. Browser Support**

- Chrome 18+
- Firefox 14+
- Safari 6+
- Edge 12+
- Polyfill: [mutationobserver-shim](https://github.com/webmodules/mutation-observer)
