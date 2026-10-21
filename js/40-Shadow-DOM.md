# Shadow DOM

## Shadow DOM: Tất Cả Những Gì Bạn Cần Biết

### 1. **Shadow DOM là gì?**

Shadow DOM là một phần của **Web Components**, cho phép bạn tạo một **DOM riêng biệt** (isolated DOM tree) trong một phần tử. Nó giúp **đóng gói (encapsulation)** HTML, CSS, JavaScript của một phần tử, ngăn không cho các styles hoặc scripts bên ngoài tác động vào nó.

### 2. **Tại sao cần Shadow DOM?**

- **Tránh xung đột CSS**: CSS bên ngoài không thể ảnh hưởng đến Shadow DOM và CSS trong Shadow DOM không ảnh hưởng ra ngoài.
- **Đóng gói (Encapsulation)**: JavaScript bên ngoài không thể truy cập trực tiếp vào Shadow DOM.
- **Tạo Component tái sử dụng**: Shadow DOM giúp tạo các UI component độc lập, dễ quản lý.

---

### 3. **Cách tạo Shadow DOM**

Có 2 loại Shadow DOM:

- **Open Shadow DOM** (có thể truy cập từ JavaScript bên ngoài).
- **Closed Shadow DOM** (không thể truy cập từ bên ngoài).

#### 3.1. **Tạo Open Shadow DOM**

```javascript
const shadowHost = document.querySelector("#my-element");
const shadowRoot = shadowHost.attachShadow({ mode: "open" }); // Open mode
shadowRoot.innerHTML = `<style> p { color: red; } </style> <p>Chào từ Shadow DOM!</p>`;
```

#### 3.2. **Tạo Closed Shadow DOM**

```javascript
const shadowHost = document.querySelector("#my-element");
const shadowRoot = shadowHost.attachShadow({ mode: "closed" }); // Closed mode
shadowRoot.innerHTML = `<p>Chào từ Closed Shadow DOM!</p>`;
console.log(shadowHost.shadowRoot); // null (không thể truy cập)
```

---

### 4. **Shadow DOM và CSS**

Shadow DOM có CSS riêng, không bị ảnh hưởng bởi CSS bên ngoài. Bạn có thể thêm `<style>` vào trong Shadow DOM để tùy chỉnh.

#### 4.1. **CSS trong Shadow DOM**

```javascript
shadowRoot.innerHTML = `
  <style>
    p { color: blue; font-size: 20px; }
  </style>
  <p>Đây là văn bản trong Shadow DOM</p>
`;
```

#### 4.2. **Shadow DOM và CSS Variables**

Shadow DOM vẫn có thể sử dụng **CSS Variables** từ `:root`.

```css
:root {
	--main-color: green;
}
```

```javascript
shadowRoot.innerHTML = `
  <style>
    p { color: var(--main-color); }
  </style>
  <p>Chào từ Shadow DOM</p>
`;
```

---

### 5. **Shadow DOM và JavaScript**

- JavaScript **bên ngoài** không thể truy cập vào các phần tử bên trong Shadow DOM nếu dùng `closed mode`.
- Dùng `querySelector()` trong Shadow Root để lấy phần tử.

#### 5.1. **Thao tác với Shadow DOM bằng JavaScript**

```javascript
const button = document.createElement("button");
button.textContent = "Click me!";
shadowRoot.appendChild(button);

button.addEventListener("click", () => {
	alert("Nút bấm trong Shadow DOM!");
});
```

---

### 6. **Light DOM vs. Shadow DOM**

**Light DOM** là phần DOM thông thường bạn viết trong HTML, còn **Shadow DOM** là DOM ẩn bên trong một phần tử.

Ví dụ về Light DOM:

```html
<div>
	<p>Đây là Light DOM</p>
</div>
```

Ví dụ về Shadow DOM:

```javascript
const shadowRoot = document.createElement("div").attachShadow({ mode: "open" });
shadowRoot.innerHTML = `<p>Đây là Shadow DOM</p>`;
```

---

### 7. **Slot: Cách Chèn Nội Dung vào Shadow DOM**

Shadow DOM có thể dùng `<slot>` để hiển thị nội dung từ Light DOM.

#### 7.1. **Ví dụ với Slot**

```html
<custom-element>
	<span slot="title">Tiêu đề</span>
	<p slot="content">Nội dung chính</p>
</custom-element>
```

```javascript
class CustomElement extends HTMLElement {
	constructor() {
		super();
		const shadow = this.attachShadow({ mode: "open" });
		shadow.innerHTML = `
      <style>
        ::slotted(span) { font-weight: bold; }
      </style>
      <h2><slot name="title"></slot></h2>
      <p><slot name="content"></slot></p>
    `;
	}
}
customElements.define("custom-element", CustomElement);
```

---

### 8. **Khi nào nên dùng Shadow DOM?**

✔ Khi tạo **Web Components** (nút bấm, modal, custom input...).  
✔ Khi muốn **CSS không bị ảnh hưởng** bởi styles từ bên ngoài.  
✔ Khi cần **tạo UI component độc lập, dễ tái sử dụng**.  
❌ Không nên dùng nếu cần tương tác nhiều với DOM bên ngoài.

---

### 9. **Kết luận**

Shadow DOM là công cụ mạnh mẽ giúp tạo các **UI components độc lập**, ngăn chặn xung đột CSS và JavaScript. Nó phù hợp cho các ứng dụng web hiện đại, giúp code dễ bảo trì và tái sử dụng.
