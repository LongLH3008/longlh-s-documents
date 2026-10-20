### **Kỹ thuật Throttle và Debounce trong Frontend**

Cả **Throttle** và **Debounce** đều là kỹ thuật giúp kiểm soát tần suất thực thi của một hàm, thường dùng để tối ưu hiệu suất trong các sự kiện xảy ra liên tục như:

- **Cuộn trang (`scroll`)**
- **Thay đổi kích thước (`resize`)**
- **Nhập dữ liệu (**`keyup`**, `keydown`, `input`)**
- **Sự kiện chuột (`mousemove`, `mouseover`, `click`)**

---

## **1️⃣ Debounce – Trì hoãn thực thi**

📌 **Định nghĩa:**  
Debounce đảm bảo rằng một hàm chỉ chạy **sau khi ngừng kích hoạt một khoảng thời gian** nhất định. Nếu sự kiện xảy ra liên tục, nó sẽ reset lại thời gian chờ.

📌 **Ứng dụng phổ biến:**

- **Tìm kiếm (search)**: Gửi request API khi người dùng ngừng gõ chữ.
- **Resize window**: Chỉ cập nhật layout sau khi người dùng dừng thay đổi kích thước cửa sổ.

📌 **Ví dụ code Debounce:**

```js
function debounce(func, delay) {
	let timeout;
	return function (...args) {
		clearTimeout(timeout); // Xóa timeout cũ nếu có
		timeout = setTimeout(() => func.apply(this, args), delay);
	};
}

// Ví dụ: Xử lý search khi người dùng dừng gõ 500ms
const handleSearch = debounce((e) => {
	console.log("Search:", e.target.value);
}, 500);

document.getElementById("search").addEventListener("input", handleSearch);
```

💡 **Giải thích:**

- Mỗi lần người dùng nhập chữ mới, `setTimeout` được reset.
- Khi người dùng ngừng nhập trong `500ms`, hàm mới thực thi.

---

## **2️⃣ Throttle – Giới hạn tần suất thực thi**

📌 **Định nghĩa:**  
Throttle đảm bảo rằng một hàm **chỉ chạy một lần trong khoảng thời gian nhất định**, bất kể có bao nhiêu lần sự kiện được kích hoạt.

📌 **Ứng dụng phổ biến:**

- **Cuộn trang (`scroll`)**: Giới hạn số lần xử lý để tránh lag.
- **Sự kiện `mousemove`, `resize`**: Cập nhật giao diện nhưng không làm ảnh hưởng đến hiệu suất.
- **Click button**: Ngăn chặn spam click quá nhanh.

📌 **Ví dụ code Throttle:**

```js
function throttle(func, delay) {
	let lastCall = 0;
	return function (...args) {
		const now = Date.now();
		if (now - lastCall >= delay) {
			lastCall = now;
			func.apply(this, args);
		}
	};
}

// Ví dụ: Xử lý scroll 300ms một lần
const handleScroll = throttle(() => {
	console.log("Scrolled at:", new Date().toLocaleTimeString());
}, 300);

window.addEventListener("scroll", handleScroll);
```

💡 **Giải thích:**

- Dù người dùng cuộn liên tục, hàm chỉ chạy **mỗi 300ms một lần**.
- Giúp giảm số lần xử lý, tối ưu hiệu suất trang web.

---

## **3️⃣ So sánh Throttle vs Debounce**

| Thuộc tính            | Debounce                                  | Throttle                                       |
| --------------------- | ----------------------------------------- | ---------------------------------------------- |
| **Cách hoạt động**    | Chỉ chạy sau một khoảng thời gian im lặng | Chạy theo chu kỳ thời gian cố định             |
| **Dùng cho**          | Input search, resize, click nhiều lần     | Scroll, mousemove, resize                      |
| **Sự kiện bị bỏ qua** | Có thể bỏ qua nhiều lần thực thi          | Giữ lại các lần thực thi theo chu kỳ           |
| **Ví dụ thực tế**     | Gửi request API sau khi user ngừng nhập   | Cập nhật giao diện khi cuộn nhưng không bị lag |

---

## **4️⃣ Kết hợp Throttle và Debounce**

Trong một số trường hợp, có thể kết hợp cả hai kỹ thuật.  
Ví dụ: Trong **tìm kiếm**, ta có thể:

- **Throttle** để kiểm soát số lần gửi API khi user nhập liên tục.
- **Debounce** để gửi API **chỉ khi user dừng gõ**.

```js
const throttledDebounce = debounce(
	throttle((e) => {
		console.log("Final API Call:", e.target.value);
	}, 300),
	500
);

document.getElementById("search").addEventListener("input", throttledDebounce);
```

**Ý nghĩa:**

- **Throttle (300ms):** Hạn chế gửi API liên tục khi user nhập.
- **Debounce (500ms):** Chờ user dừng gõ rồi gửi API.

---

🔹 **Tóm lại:**

- **Dùng debounce** khi cần đợi người dùng **ngừng thao tác**.
- **Dùng throttle** khi cần **giới hạn số lần thực thi**.
- **Có thể kết hợp cả hai** nếu cần tối ưu hơn.
