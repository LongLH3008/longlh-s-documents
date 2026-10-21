# DOM Dimensions

**DOM Dimensions** (Kích thước phần tử trong DOM) bao gồm tất cả các phương thức và thuộc tính có thể sử dụng để lấy thông tin về kích thước và vị trí của phần tử trên trang web. Đây là những công cụ quan trọng trong việc xây dựng giao diện động và responsive. Các thuộc tính này giúp đo lường kích thước của phần tử, tính toán vị trí của chúng và xác định sự thay đổi trong layout.

### 1. **Kích thước Phần Tử (Element Dimensions)**

#### **`clientWidth` và `clientHeight`**

- **`clientWidth`**: Chiều rộng của phần tử, tính cả `padding` nhưng không tính `border`, `margin`, hoặc `scrollbar`.
- **`clientHeight`**: Chiều cao của phần tử, tính cả `padding` nhưng không tính `border`, `margin`, hoặc `scrollbar`.

> **Sử dụng**: Dùng để lấy kích thước hiển thị của phần tử (phần nhìn thấy được trong container, không tính các phần bị ẩn vì overflow hoặc thanh cuộn).

```js
const element = document.getElementById("myElement");
console.log(element.clientWidth, element.clientHeight);
```

#### **`scrollWidth` và `scrollHeight`**

- **`scrollWidth`**: Chiều rộng của phần tử, bao gồm cả phần nội dung bị ẩn do `overflow`.
- **`scrollHeight`**: Chiều cao của phần tử, bao gồm cả phần nội dung bị ẩn do `overflow`.

> **Sử dụng**: Dùng để kiểm tra tổng chiều rộng hoặc chiều cao của nội dung bên trong phần tử (kể cả phần không thể nhìn thấy nếu có `overflow`).

```js
const element = document.getElementById("myElement");
console.log(element.scrollWidth, element.scrollHeight);
```

#### **`offsetWidth` và `offsetHeight`**

- **`offsetWidth`**: Chiều rộng của phần tử bao gồm `padding`, `border`, và `scrollbar`, nhưng không tính `margin`.
- **`offsetHeight`**: Chiều cao của phần tử bao gồm `padding`, `border`, và `scrollbar`, nhưng không tính `margin`.

> **Sử dụng**: Dùng để lấy kích thước thực tế của phần tử trong layout, có tính đến thanh cuộn và biên giới (border).

```js
const element = document.getElementById("myElement");
console.log(element.offsetWidth, element.offsetHeight);
```

---

### 2. **Kích thước Cửa Sổ Trình Duyệt (Viewport Dimensions)**

#### **`innerWidth` và `innerHeight`**

- **`innerWidth`**: Chiều rộng của cửa sổ trình duyệt, bao gồm cả thanh cuộn ngang nếu có.
- **`innerHeight`**: Chiều cao của cửa sổ trình duyệt, bao gồm cả thanh cuộn dọc nếu có.

> **Sử dụng**: Dùng để lấy kích thước của cửa sổ trình duyệt. Rất hữu ích trong việc làm giao diện responsive, giúp điều chỉnh layout khi cửa sổ thay đổi kích thước.

```js
console.log(window.innerWidth, window.innerHeight);
```

#### **`outerWidth` và `outerHeight`**

- **`outerWidth`**: Chiều rộng của cửa sổ trình duyệt bao gồm cả thanh công cụ và các thanh tiêu đề.
- **`outerHeight`**: Chiều cao của cửa sổ trình duyệt bao gồm cả thanh công cụ và các thanh tiêu đề.

> **Sử dụng**: Thường được dùng khi muốn lấy kích thước của toàn bộ cửa sổ trình duyệt, bao gồm các phần ngoài content (như thanh công cụ, cửa sổ tiêu đề của trình duyệt).

```js
console.log(window.outerWidth, window.outerHeight);
```

---

### 3. **Vị Trí Phần Tử (Element Positioning)**

#### **`getBoundingClientRect()`**

- Phương thức này trả về một đối tượng có các thuộc tính **`top`**, **`right`**, **`bottom`**, **`left`**, **`width`**, và **`height`** của phần tử, tính từ **viewport** (khung nhìn của cửa sổ trình duyệt).

> **Sử dụng**: Dùng để lấy vị trí và kích thước chính xác của phần tử so với cửa sổ trình duyệt.

```js
const element = document.getElementById("myElement");
const rect = element.getBoundingClientRect();
console.log(rect.top, rect.left, rect.width, rect.height);
```

---

### 4. **Phương Thức & Thuộc Tính Liên Quan**

#### **`window.scrollX` và `window.scrollY`**

- **`scrollX`**: Khoảng cách cuộn theo chiều ngang của cửa sổ trình duyệt.
- **`scrollY`**: Khoảng cách cuộn theo chiều dọc của cửa sổ trình duyệt.

> **Sử dụng**: Dùng để biết vị trí cuộn của cửa sổ hiện tại, thường dùng khi cần tính toán vị trí của phần tử khi cuộn trang.

```js
console.log(window.scrollX, window.scrollY);
```

---

### 5. **Lý Thuyết Tóm Tắt**

| Thuộc tính                | Mô tả                                                | Bao gồm phần        | Khi nào sử dụng                         |
| ------------------------- | ---------------------------------------------------- | ------------------- | --------------------------------------- |
| `clientWidth`             | Chiều rộng hiển thị của phần tử, không tính `border` | `padding`           | Kiểm tra kích thước hiển thị            |
| `clientHeight`            | Chiều cao hiển thị của phần tử, không tính `border`  | `padding`           | Kiểm tra kích thước hiển thị            |
| `scrollWidth`             | Chiều rộng toàn bộ nội dung, kể cả phần ẩn           | `padding`           | Kiểm tra nếu nội dung bị cắt            |
| `scrollHeight`            | Chiều cao toàn bộ nội dung, kể cả phần ẩn            | `padding`           | Kiểm tra nếu nội dung bị cắt            |
| `offsetWidth`             | Chiều rộng thực tế của phần tử, bao gồm `border`     | `padding`, `border` | Kiểm tra kích thước layout              |
| `offsetHeight`            | Chiều cao thực tế của phần tử, bao gồm `border`      | `padding`, `border` | Kiểm tra kích thước layout              |
| `innerWidth`              | Chiều rộng của cửa sổ trình duyệt (viewport)         | N/A                 | Kiểm tra kích thước cửa sổ              |
| `innerHeight`             | Chiều cao của cửa sổ trình duyệt (viewport)          | N/A                 | Kiểm tra kích thước cửa sổ              |
| `getBoundingClientRect()` | Vị trí và kích thước của phần tử so với viewport     | Tất cả              | Xác định vị trí và kích thước chính xác |
| `scrollX`                 | Khoảng cách cuộn theo chiều ngang của cửa sổ         | N/A                 | Kiểm tra vị trí cuộn                    |
| `scrollY`                 | Khoảng cách cuộn theo chiều dọc của cửa sổ           | N/A                 | Kiểm tra vị trí cuộn                    |

---

### 6. **Ứng Dụng Thực Tế**

- **Responsive Design**: Điều chỉnh giao diện dựa trên kích thước cửa sổ trình duyệt (`innerWidth`, `innerHeight`).
- **Làm việc với Scroll**: Kiểm tra khi nào nội dung bị cuộn hoặc ẩn (`scrollHeight`, `scrollWidth`, `scrollX`, `scrollY`).
- **Layout & Positioning**: Xác định vị trí của phần tử trong viewport (`getBoundingClientRect`).

---

**DOM Dimensions** giúp đo lường và tính toán kích thước của phần tử trong DOM, hỗ trợ việc xây dựng giao diện web và xử lý layout động. Chúng rất hữu ích khi làm việc với các thao tác liên quan đến việc thay đổi hoặc tính toán chiều cao, chiều rộng, và vị trí của các phần tử khi thay đổi kích thước cửa sổ trình duyệt hoặc cuộn trang.
