# Sparse Array

### 🔍 **Mảng thưa (Sparse Array) trong JavaScript**

Mảng thưa (**Sparse Array**) là mảng có một hoặc nhiều phần tử bị **bỏ trống** (**empty slots**) thay vì chứa giá trị cụ thể như `undefined` hoặc một giá trị nào đó.

---

## 🧐 **1. Ví dụ về mảng thưa**

Khi bạn khai báo một mảng nhưng không gán giá trị cho một số phần tử, nó sẽ trở thành **mảng thưa**:

```js
const arr = [1, , , 4]; // Mảng có 2 phần tử trống
console.log(arr); // [1, empty × 2, 4]
```

📌 **Giải thích:**

- `arr[0]` có giá trị `1`
- `arr[1]` và `arr[2]` là **empty slots**
- `arr[3]` có giá trị `4`

---

## ⚡ **2. Đặc điểm của mảng thưa**

### 🛑 **Không có giá trị `undefined` thực sự**

```js
const arr = [, , 4];

console.log(arr[0]); // undefined (nhưng không phải là giá trị thực sự)
console.log(arr.hasOwnProperty(0)); // false (không tồn tại key 0)
console.log(0 in arr); // false (index 0 không có trong mảng)
```

🔹 **Lưu ý:** Empty slot khác với `undefined`. Nếu bạn gán `undefined`, nó vẫn được coi là một phần tử hợp lệ.

```js
const arr2 = [undefined, undefined, 4];

console.log(arr2.hasOwnProperty(0)); // true (tồn tại key 0)
console.log(0 in arr2); // true
```

---

## 🔄 **3. Vòng lặp và mảng thưa**

### ✅ `forEach()` **bỏ qua empty slots**

```js
[, , 4].forEach((value, index) => console.log(index, value));
// Kết quả: 2 4
```

### ✅ `for...in` **chỉ lặp qua phần tử có giá trị thực**

```js
for (let index in [, , 4]) console.log(index);
// Kết quả: 2
```

### ✅ `for...of` **bỏ qua empty slots**

```js
for (let value of [, , 4]) console.log(value);
// Kết quả: 4
```

### ✅ `map()` **bỏ qua empty slots**

```js
console.log([, , 4].map((x) => x * 2));
// Kết quả: [empty × 2, 8]
```

### 🚫 `length` **bao gồm cả empty slots**

```js
console.log([, , 4].length); // 3
```

---

## 🔥 **4. Cách tạo mảng thưa**

Có nhiều cách để tạo mảng thưa:

### ✅ Cách 1: Dùng dấu `,` khi khai báo

```js
const sparseArray = [1, , , 4];
```

### ✅ Cách 2: Dùng `new Array()` mà không gán giá trị

```js
const sparseArray = new Array(5);
console.log(sparseArray); // [empty × 5]
```

---

## ✅ **5. Cách tránh lỗi do mảng thưa**

Mảng thưa có thể gây lỗi khi sử dụng với một số phương thức mảng. Bạn có thể:

1. **Dùng `fill()` để tránh empty slots**
      ```js
      const arr = new Array(5).fill(null);
      console.log(arr); // [null, null, null, null, null]
      ```
2. **Dùng `Array.from()` để loại bỏ empty slots**

      ```js
      const arr = Array.from([, , 4]);
      console.log(arr); // [undefined, undefined, 4]
      ```

3. **Dùng `filter()` để bỏ qua empty slots**
      ```js
      const arr = [, , 4].filter(() => true);
      console.log(arr); // [4]
      ```

---

## 🚀 **Tóm lại**

- **Mảng thưa (Sparse Array)** là mảng có phần tử trống (empty slots)
- **Empty slots không giống với `undefined`**
- **Một số phương thức như `forEach()`, `map()` sẽ bỏ qua empty slots**
- **Nên tránh sử dụng mảng thưa để tránh lỗi khó debug**

Bạn nên dùng **`fill()`**, **`Array.from()`**, hoặc **`filter()`** để đảm bảo dữ liệu mảng không bị rỗng. 🚀
