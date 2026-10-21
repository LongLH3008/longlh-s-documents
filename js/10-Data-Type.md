# Data Type

JavaScript có hai nhóm kiểu dữ liệu chính: **Primitive (nguyên thủy)** và **Non-primitive (không nguyên thủy - tham chiếu)**.

---

## 📌 **1. Kiểu dữ liệu nguyên thủy (Primitive Types)**

✅ **Lưu trữ trực tiếp giá trị trong bộ nhớ**  
✅ **Không thể thay đổi giá trị (Immutable)**  
✅ **So sánh bằng giá trị**

| Kiểu dữ liệu    | Mô tả                                                |
| --------------- | ---------------------------------------------------- |
| **`Number`**    | Đại diện cho cả số nguyên (`5`) và số thực (`3.14`). |
| **`BigInt`**    | Dùng để lưu trữ số lớn hơn giới hạn của `Number`.    |
| **`String`**    | Chuỗi ký tự (`"Hello"`, `'JS'`).                     |
| **`Boolean`**   | Chỉ có hai giá trị `true` hoặc `false`.              |
| **`Undefined`** | Biến được khai báo nhưng chưa được gán giá trị.      |
| **`Null`**      | Biểu thị một giá trị "không có gì".                  |
| **`Symbol`**    | Tạo giá trị duy nhất, không thể trùng lặp.           |

### 📝 **Ví dụ:**

```js
let a = 42; // Number
let big = 123n; // BigInt
let name = "JS"; // String
let isReady = true; // Boolean
let data; // Undefined
let empty = null; // Null
let sym = Symbol(); // Symbol
```

---

## 📌 **2. Kiểu dữ liệu không nguyên thủy (Non-Primitive / Reference Types)**

✅ **Lưu trữ tham chiếu đến giá trị trong bộ nhớ**  
✅ **Có thể thay đổi giá trị (Mutable)**  
✅ **So sánh bằng tham chiếu**

| Kiểu dữ liệu   | Mô tả                                                               |
| -------------- | ------------------------------------------------------------------- |
| **`Object`**   | Tập hợp key-value (`{}`), có thể chứa nhiều kiểu dữ liệu khác nhau. |
| **`Array`**    | Danh sách các phần tử (`[]`), có thể chứa nhiều kiểu dữ liệu.       |
| **`Function`** | Hàm trong JavaScript (`function() {}`).                             |
| **`Date`**     | Xử lý thời gian, ngày tháng.                                        |
| **`RegExp`**   | Biểu thức chính quy để tìm kiếm hoặc thay thế trong chuỗi.          |
| **`Set`**      | Tập hợp các giá trị duy nhất.                                       |
| **`Map`**      | Lưu trữ key-value nhưng có thể dùng bất kỳ kiểu dữ liệu làm key.    |

### 📝 **Ví dụ:**

```js
let obj = { name: "John", age: 30 }; // Object
let arr = [1, 2, 3]; // Array
function sayHello() {
	console.log("Hi");
} // Function
let date = new Date(); // Date
let regex = /abc/i; // RegExp
let set = new Set([1, 2, 3]); // Set
let map = new Map([["key", "value"]]); // Map
```

---

## 📌 **3. So sánh giữa Primitive vs Non-Primitive**

| Đặc điểm         | Kiểu nguyên thủy (Primitive) | Kiểu tham chiếu (Non-Primitive) |
| ---------------- | ---------------------------- | ------------------------------- |
| Lưu trữ          | Trực tiếp trong bộ nhớ       | Lưu tham chiếu đến bộ nhớ       |
| Có thể thay đổi? | ❌ Không thể thay đổi        | ✅ Có thể thay đổi              |
| So sánh          | So sánh theo giá trị         | So sánh theo tham chiếu         |

### 📝 **Ví dụ:**

```js
// So sánh Primitive (so sánh giá trị)
console.log(10 === 10); // true

// So sánh Non-Primitive (so sánh tham chiếu)
let obj1 = { a: 1 };
let obj2 = { a: 1 };
console.log(obj1 === obj2); // false (vì là 2 object khác nhau trong bộ nhớ)
```

---

💡 **Tóm lại:**

- **Primitive types**: `Number`, `BigInt`, `String`, `Boolean`, `Undefined`, `Null`, `Symbol`.
- **Non-Primitive types**: `Object`, `Array`, `Function`, `Date`, `RegExp`, `Set`, `Map`.

📌 **Nắm vững sự khác biệt giữa giá trị và tham chiếu rất quan trọng khi làm việc với JavaScript!** 🚀
