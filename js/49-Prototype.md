# 1. Prototype là gì?

Prototype trong JavaScript là một object mà từ đó các object khác có thể kế thừa thuộc tính và phương thức thông qua cơ chế gọi là prototype chain.

Trong JavaScript, **mọi object đều có một “prototype”** – tức là một object khác mà nó “kế thừa” thuộc tính/phương thức từ đó.

👉 Nói đơn giản:

> Prototype = cơ chế **kế thừa (inheritance)** trong JavaScript

---

# 2. Prototype chain (chuỗi prototype)

Khi bạn truy cập một property:

```js
obj.a;
```

JS sẽ tìm theo thứ tự:

1. Tìm trong `obj`
2. Không có → tìm trong `obj.__proto__`
3. Không có → tìm tiếp lên trên nữa
4. Đến `null` thì dừng

👉 Đây gọi là **prototype chain**

---

# 3. **proto** vs prototype (rất hay hỏi)

### 3.1 `__proto__`

- Có trong **mọi object**
- Trỏ tới prototype của object đó

```js
const obj = {};
console.log(obj.__proto__); // Object.prototype
```

---

### 3.2 `prototype`

- Chỉ có trong **function constructor**
- Dùng để tạo prototype cho object mới

```js
function Person() {}
console.log(Person.prototype);
```

---

👉 Quan hệ quan trọng:

```js
obj.__proto__ === Constructor.prototype;
```

---

# 4. Constructor function + prototype

```js
function Person(name) {
	this.name = name;
}

Person.prototype.sayHello = function () {
	console.log("Hello " + this.name);
};

const p1 = new Person("Long");
p1.sayHello();
```

---

### Khi dùng `new Person()`:

JS làm 4 bước:

1. Tạo object rỗng `{}`
2. Gán:

      ```js
      obj.__proto__ = Person.prototype;
      ```

3. Gọi function `Person` với `this = obj`
4. Return object

---

# 5. Ví dụ trực quan

```js
const arr = [1, 2, 3];

arr.__proto__ === Array.prototype; // true
Array.prototype.__proto__ === Object.prototype; // true
Object.prototype.__proto__ === null;
```

👉 Chain:

```
arr → Array.prototype → Object.prototype → null
```

---

# 6. Prototype giúp tiết kiệm bộ nhớ

❌ Sai cách:

```js
function Person(name) {
	this.name = name;
	this.sayHello = function () {
		console.log("Hi");
	};
}
```

👉 Mỗi instance có 1 function riêng (tốn memory)

---

✅ Đúng cách:

```js
function Person(name) {
	this.name = name;
}

Person.prototype.sayHello = function () {
	console.log("Hi");
};
```

👉 Tất cả instance dùng chung 1 function

---

# 7. hasOwnProperty

Kiểm tra property thuộc object hay từ prototype

```js
const obj = { a: 1 };

console.log(obj.hasOwnProperty("a")); // true
console.log(obj.hasOwnProperty("toString")); // false
```

---

# 8. Shadowing (ghi đè prototype)

```js
const obj = { a: 1 };

obj.__proto__ = { a: 2 };

console.log(obj.a); // 1 (ưu tiên object trước)
```

---

# 9. Object.create()

Tạo object với prototype chỉ định:

```js
const parent = {
	greet() {
		console.log("Hi");
	},
};

const child = Object.create(parent);

child.greet(); // Hi
```

---

# 10. ES6 class thực chất là gì?

```js
class Person {
	constructor(name) {
		this.name = name;
	}

	sayHello() {
		console.log("Hi");
	}
}
```

👉 Thực chất:

```js
Person.prototype.sayHello = function () {};
```

➡️ `class` chỉ là **syntactic sugar của prototype**

---

# 11. Inheritance (kế thừa)

```js
function Animal(name) {
	this.name = name;
}

Animal.prototype.say = function () {
	console.log("Animal");
};

function Dog(name) {
	Animal.call(this, name);
}

Dog.prototype = Object.create(Animal.prototype);

Dog.prototype.bark = function () {
	console.log("Woof");
};

const d = new Dog("Tom");
d.say(); // Animal
d.bark(); // Woof
```

---

# 12. Những câu hỏi phỏng vấn hay gặp

### ❓ Prototype là gì?

→ Cơ chế kế thừa qua chain

---

### ❓ `__proto__` vs `prototype`?

→ Object vs Function

---

### ❓ Class có phải OOP thật không?

→ Không, chỉ là wrapper của prototype

---

### ❓ Lookup property diễn ra như thế nào?

→ Theo prototype chain

---

### ❓ Tại sao dùng prototype?

→ Tiết kiệm memory + reuse function

---

# 13. Sơ đồ tổng quát

```
instance
   ↓ __proto__
Constructor.prototype
   ↓
Object.prototype
   ↓
null
```

---

# 14. Một số lưu ý quan trọng

- Không nên dùng `__proto__` trong production → dùng:

     ```js
     Object.getPrototypeOf();
     Object.setPrototypeOf();
     ```

- Function cũng là object → cũng có prototype chain
- Arrow function **không có prototype**

---

# 15. Tổng kết (ngắn gọn để nhớ)

- Prototype = cơ chế kế thừa
- `__proto__` = link nội bộ của object
- `prototype` = thuộc về constructor
- Lookup chạy theo chain
- Class = sugar của prototype
