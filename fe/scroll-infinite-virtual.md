## 1️⃣ **Virtual Scroll (Cuộn ảo)**

Virtual Scroll giúp hiển thị một danh sách lớn mà chỉ render một phần nhỏ để tối ưu hiệu suất.

### **Cách hoạt động:**

- Chỉ render số lượng item vừa đủ nhìn thấy trên màn hình.
- Cập nhật lại danh sách hiển thị khi cuộn.

```html
<!DOCTYPE html>
<html lang="vi">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Virtual Scroll</title>
		<style>
			#container {
				height: 300px;
				overflow-y: auto;
				border: 1px solid #ccc;
				position: relative;
			}

			#list {
				position: relative;
				width: 100%;
			}

			.item {
				height: 50px;
				display: flex;
				align-items: center;
				padding: 0 10px;
				border-bottom: 1px solid #ddd;
				position: absolute;
				/* Cần có để áp dụng top */
				width: 100%;
				background: white;
			}
		</style>
	</head>

	<body>
		<div id="container">
			<div id="list"></div>
		</div>

		<script>
			const totalItems = 10000; // Tổng số item
			const itemHeight = 50; // Chiều cao mỗi item
			const container = document.getElementById("container");
			const list = document.getElementById("list");

			list.style.height = `${totalItems * itemHeight}px`; // Đặt chiều cao list để tạo scroll

			function updateVisibleItems() {
				const scrollTop = container.scrollTop;
				const visibleCount = Math.ceil(container.clientHeight / itemHeight);
				const startIndex = Math.floor(scrollTop / itemHeight);
				const endIndex = Math.min(startIndex + visibleCount, totalItems);

				list.innerHTML = ""; // Xóa item cũ nhưng giữ khoảng trắng

				// Tạo padding-top để giữ khoảng trống phía trên
				const spacer = document.createElement("div");
				spacer.style.height = `${startIndex * itemHeight}px`;
				list.appendChild(spacer);

				for (let i = startIndex; i < endIndex; i++) {
					const item = document.createElement("div");
					item.className = "item";
					item.innerText = `Item ${i + 1}`;
					item.style.top = `${i * itemHeight}px`;
					list.appendChild(item);
				}
			}

			container.addEventListener("scroll", updateVisibleItems);
			updateVisibleItems(); // Load danh sách ban đầu
		</script>
	</body>
</html>
```

---

## 2️⃣ **Infinite Scroll (Cuộn vô hạn)**

Infinite Scroll giúp tải dữ liệu động khi cuộn xuống cuối trang.

### **Cách hoạt động:**

- Khi cuộn đến cuối danh sách, tải thêm dữ liệu.
- Không xóa dữ liệu cũ mà chỉ thêm vào danh sách.

```html
<!DOCTYPE html>
<html lang="vi">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Infinite Scroll</title>
		<style>
			#container {
				height: 400px;
				overflow-y: auto;
				border: 1px solid #ccc;
			}
			.item {
				height: 50px;
				display: flex;
				align-items: center;
				padding: 0 10px;
				border-bottom: 1px solid #ddd;
			}
		</style>
	</head>
	<body>
		<div id="container">
			<div id="list"></div>
		</div>

		<script>
			const container = document.getElementById("container");
			const list = document.getElementById("list");

			let itemCount = 20;
			const itemHeight = 50;
			const maxItems = 100; // Giới hạn số lượng item tối đa để demo

			function addItems(count) {
				for (let i = 0; i < count; i++) {
					const item = document.createElement("div");
					item.className = "item";
					item.innerText = `Item ${list.children.length + 1}`;
					list.appendChild(item);
				}
			}

			function onScroll() {
				if (container.scrollTop + container.clientHeight >= container.scrollHeight - 10) {
					if (list.children.length < maxItems) {
						addItems(10);
					}
				}
			}

			container.addEventListener("scroll", onScroll);
			addItems(itemCount); // Load danh sách ban đầu
		</script>
	</body>
</html>
```

---

### 🔹 **So sánh Virtual Scroll vs. Infinite Scroll**

| Tính năng                    | Virtual Scroll | Infinite Scroll |
| ---------------------------- | -------------- | --------------- |
| Tải từng phần nhỏ            | ✅             | ❌              |
| Xóa các item không cần thiết | ✅             | ❌              |
| Dữ liệu lớn tối ưu hơn       | ✅             | ❌              |
| Cuộn liên tục để tải thêm    | ❌             | ✅              |

💡 **Khi nào dùng gì?**

- Dùng **Virtual Scroll** nếu danh sách quá lớn (hàng nghìn/millions item).
- Dùng **Infinite Scroll** nếu muốn tải động dữ liệu từ API khi cuộn.

🔥 **Gợi ý:** Nếu dùng dữ liệu từ API, có thể kết hợp **Infinite Scroll với Intersection Observer** để tối ưu việc gọi API.
