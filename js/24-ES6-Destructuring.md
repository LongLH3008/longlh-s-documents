# ES6: Destructuring

## Giới thiệu

[ DESTRUCTURING ]

Destructuring là một tính năng quan trọng trong ECMAScript 2015 (ES6) cho phép bạn rút (extract) 
các giá trị từ các cấu trúc dữ liệu như mảng và đối tượng và gán chúng cho biến riêng lẻ một cách 
dễ dàng. 
Destructuring làm cho việc truy xuất dữ liệu từ cấu trúc này trở nên ngắn gọn và dễ đọc hơn.

---

## Array

```js
// ---------------------------------------------------------------------------------------------------------------------
// ---------- (Array Destructuring) -----------------------------------------------------------------------------------------------------------
// ---------------------------------------------------------------------------------------------------------------------

// Destructuring với Mảng (Array Destructuring)
// Rút giá trị từ mảng

const numbers = [1, 2, 3, 4, 5];
const [first, second] = numbers;

console.log(first); // Kết quả: 1
console.log(second); // Kết quả: 2

// ---------------------------------------------------------------------------------------------------------------------

// Destructuring với Mảng (Array Destructuring): 
// Bỏ qua giá trị không cần thiết

const numbers2 = [1, 2, 3, 4, 5];
const [, , third] = numbers2;

console.log(third); // Kết quả: 3

// ---------------------------------------------------------------------------------------------------------------------

// Destructuring với Mảng (Array Destructuring)
// Dự phòng với giá trị mặc định:

const numbers3 = [1];
const [first2, second2 = 2] = numbers3;

console.log(first2); // Kết quả: 1
console.log(second2); // Kết quả: 2
```

---

## Object

```js
// ---------------------------------------------------------------------------------------------------------------------
// --------- (Object Destructuring) ------------------------------------------------------------------------------------------------------------
// ---------------------------------------------------------------------------------------------------------------------

// Destructuring với Đối tượng (Object Destructuring):
// Rút giá trị từ đối tượng:

const person = { name2: 'John', age: 30 };
const { name2, age2 } = person;

console.log(name2); // Kết quả: 'John'
console.log(age2); // Kết quả: 30

// ---------------------------------------------------------------------------------------------------------------------

// Destructuring với Đối tượng (Object Destructuring):
// Sử dụng tên biến khác cho giá trị được rút:

const person2 = { name: 'John', age: 30 };
const { name: fullName, age: years } = person2;

console.log(fullName); // Kết quả: 'John'
console.log(years); // Kết quả: 30

// ---------------------------------------------------------------------------------------------------------------------

// Destructuring với Đối tượng (Object Destructuring):
// Dự phòng với giá trị mặc định:

const person3 = { name3: 'John' };
const { name3, age3 = 25 } = person3;

console.log(name3); // Kết quả: 'John'
console.log(age3); // Kết quả: 25 (giá trị mặc định được sử dụng)
```

---

## Function

```js
// ---------------------------------------------------------------------------------------------------------------------
// ---------- (Function Destructuring) -----------------------------------------------------------------------------------------------------------
// ---------------------------------------------------------------------------------------------------------------------

// Destructuring cũng có thể được sử dụng trong tham số hàm để rút giá trị từ đối tượng hoặc mảng.

function printName({ name }) {
  console.log(name);
}

const person = { name: 'Alice', age: 28 };
printName(person); // Kết quả: 'Alice'
```
