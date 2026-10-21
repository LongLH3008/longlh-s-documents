# Event Delegation

**Event Delegation trong JavaScript** là một kỹ thuật tối ưu xử lý sự kiện (event handling) giúp bạn:

- **Giảm số lượng listener** gắn vào DOM.
- **Tối ưu hiệu suất** với các element được tạo động.
- **Tăng tính linh hoạt** trong quản lý sự kiện.

---

## ✅ 1. **Event Delegation là gì?**

Event Delegation là **cách gắn event listener vào một phần tử cha** thay vì từng phần tử con. Khi sự kiện xảy ra, **event sẽ “bubbling” (nổi lên)** qua các phần tử cha, cho phép bạn xử lý sự kiện từ cha.

---

## ✅ 2. **Ví dụ cơ bản**

Giả sử bạn có danh sách `<ul>` gồm nhiều `<li>`:

```html
<ul id="menu">
	<li>Home</li>
	<li>About</li>
	<li>Contact</li>
</ul>
```

### ❌ Cách không dùng delegation:

```js
document.querySelectorAll("#menu li").forEach((item) => {
	item.addEventListener("click", () => {
		console.log(item.textContent);
	});
});
```

### ✅ Cách dùng Event Delegation:

```js
document.getElementById("menu").addEventListener("click", (e) => {
	if (e.target.tagName === "LI") {
		console.log(e.target.textContent);
	}
});
```

> 🎯 Gắn 1 lần duy nhất cho `ul`, nhưng vẫn xử lý được click từ tất cả `li`, kể cả những `li` được thêm sau đó!

---

## ✅ 3. **Cách hoạt động: Event Bubbling**

- Khi bạn click vào `li`, event **bubble lên** đến `ul`.
- `ul` có listener → xử lý được event của `li`.

---

## ✅ 4. **Tại sao dùng Event Delegation?**

| Lý do              | Giải thích                              |
| ------------------ | --------------------------------------- |
| ✅ Hiệu suất       | Giảm số lượng event listeners           |
| ✅ Tối ưu DOM động | Các phần tử thêm vào sau vẫn được xử lý |
| ✅ Dễ quản lý      | Gắn và xóa listener dễ hơn              |

---

## ✅ 5. **Lưu ý khi dùng**

- **Dùng `e.target`** để xác định phần tử thực sự được click.
- **So sánh bằng `e.target.matches(selector)`** để xác định element con cụ thể.
- Có thể kết hợp với **`closest()`** nếu cần tìm ancestor cụ thể.

```js
document.getElementById("menu").addEventListener("click", (e) => {
	const li = e.target.closest("li");
	if (li) {
		console.log(li.textContent);
	}
});
```

---

## ✅ 6. **Ứng dụng thực tế**

- Xử lý click cho danh sách sản phẩm động.
- Menu dropdown.
- Bảng dữ liệu (datatable).
- Chơi game (canvas HTML).
- Quản lý các button thêm/xóa trong form.

---

## ✅ 7. **Nhược điểm**

- Không hoạt động nếu phần tử không bubble (`blur`, `focus`, `mouseenter`, `mouseleave`, `scroll`, v.v.).
- Logic phân biệt target cần chính xác, nếu không dễ gây bug.

---

## ✅ 8. **So sánh nhanh**

| Tiêu chí             | Gắn trực tiếp | Event Delegation |
| -------------------- | ------------- | ---------------- |
| Gắn nhiều listener   | Có            | Không            |
| Phù hợp phần tử động | Không         | Có               |
| Hiệu suất            | Thấp hơn      | Cao hơn          |

---

Nếu bạn muốn mình ví dụ về các tình huống cụ thể như "todolist", "form nhập liệu", "bảng dữ liệu", mình có thể code luôn minh họa nhé.
