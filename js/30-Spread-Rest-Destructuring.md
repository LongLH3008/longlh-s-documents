# **Tất cả về Spread, Rest và Destructuring trong JavaScript**

## **1. Spread Operator (`...`)**

Spread (`...`) dùng để **sao chép hoặc mở rộng** một mảng, đối tượng hoặc chuỗi.

### **1.1. Sao chép mảng**

```javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1]; // Sao chép mảng
console.log(arr2); // [1, 2, 3]
```

### **1.2. Nối mảng**

```javascript
const arrA = [1, 2];
const arrB = [3, 4];
const arrC = [...arrA, ...arrB];
console.log(arrC); // [1, 2, 3, 4]
```

### **1.3. Sao chép đối tượng**

```javascript
const obj1 = { name: "Alice", age: 25 };
const obj2 = { ...obj1 };
console.log(obj2); // { name: "Alice", age: 25 }
```

### **1.4. Gộp đối tượng**

```javascript
const objA = { name: "Alice" };
const objB = { age: 25 };
const objC = { ...objA, ...objB };
console.log(objC); // { name: "Alice", age: 25 }
```

### **1.5. Truyền tham số cho hàm**

```javascript
function sum(a, b, c) {
	return a + b + c;
}
const numbers = [1, 2, 3];
console.log(sum(...numbers)); // 6
```

---

## **2. Rest Parameter (`...`)**

Rest (`...`) dùng để **gom nhóm** nhiều giá trị thành một mảng.

### **2.1. Rest trong tham số hàm**

```javascript
function sumAll(...numbers) {
	return numbers.reduce((acc, num) => acc + num, 0);
}
console.log(sumAll(1, 2, 3, 4)); // 10
```

✅ **Lợi ích:** Cho phép truyền **bất kỳ số lượng tham số nào** vào hàm.

### **2.2. Rest trong destructuring**

```javascript
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(first, second); // 1, 2
console.log(rest); // [3, 4, 5]
```

---

## **3. Destructuring (Phân rã)**

Destructuring giúp **trích xuất giá trị** từ mảng hoặc đối tượng một cách nhanh chóng.

### **3.1. Destructuring với mảng**

```javascript
const numbers = [10, 20, 30];
const [a, b, c] = numbers;
console.log(a, b, c); // 10 20 30
```

⏳ **Bỏ qua phần tử không cần thiết**

```javascript
const nums = [1, 2, 3, 4];
const [, x, , y] = nums;
console.log(x, y); // 2, 4
```

⏳ **Sử dụng với Rest**

```javascript
const [first, ...others] = [100, 200, 300, 400];
console.log(first); // 100
console.log(others); // [200, 300, 400]
```

---

### **3.2. Destructuring với Object**

```javascript
const person = { name: "John", age: 30 };
const { name, age } = person;
console.log(name, age); // John 30
```

⏳ **Đổi tên biến**

```javascript
const user = { username: "alice", email: "alice@mail.com" };
const { username: name, email: contact } = user;
console.log(name, contact); // alice alice@mail.com
```

⏳ **Giá trị mặc định**

```javascript
const student = { name: "Bob" };
const { name, age = 18 } = student;
console.log(name, age); // Bob 18
```

⏳ **Destructuring trong tham số hàm**

```javascript
function printUser({ name, age }) {
	console.log(`Tên: ${name}, Tuổi: ${age}`);
}
const userInfo = { name: "David", age: 40 };
printUser(userInfo); // Tên: David, Tuổi: 40
```

---

## **4. So sánh Spread vs. Rest vs. Destructuring**

| **Tính năng**      | **Spread (`...`)**                                        | **Rest (`...`)**                      | **Destructuring**                        |
| ------------------ | --------------------------------------------------------- | ------------------------------------- | ---------------------------------------- |
| **Chức năng**      | Mở rộng mảng/đối tượng                                    | Gom nhóm nhiều giá trị                | Trích xuất giá trị từ mảng/đối tượng     |
| **Vị trí sử dụng** | Dùng khi truyền tham số, sao chép hoặc gộp mảng/đối tượng | Dùng trong tham số hàm, destructuring | Dùng khi khai báo biến từ mảng/đối tượng |
| **Ví dụ**          | `[...arr]`, `{...obj}`                                    | `(...args)`, `[a, ...rest]`           | `const {name} = obj`, `[a, b] = arr`     |

---

## **5. Kết luận**

- **Spread (`...`)**: Dùng để **mở rộng mảng/đối tượng** hoặc truyền tham số.
- **Rest (`...`)**: Dùng để **gom nhóm nhiều giá trị thành mảng**.
- **Destructuring**: Dùng để **trích xuất giá trị** từ mảng/đối tượng một cách nhanh chóng.

💡 **Ghi nhớ:**

- `...` là **spread** khi **mở rộng** mảng/đối tượng.
- `...` là **rest** khi **gom nhóm** phần còn lại của mảng/đối tượng.
- **Destructuring** giúp **trích xuất giá trị dễ dàng** hơn.

🚀 **Nắm vững ba kỹ thuật này sẽ giúp code của bạn gọn gàng và hiệu quả hơn!** 🎯
