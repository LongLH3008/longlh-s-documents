# OOP

## OOP

## Lập trình hướng đối tượng (OOP) trong JavaScript

**Lập trình hướng đối tượng (OOP - Object-Oriented Programming)** là một mô hình lập trình dựa trên khái niệm về **đối tượng (object)** để tổ chức code một cách hiệu quả, dễ bảo trì và mở rộng. JavaScript hỗ trợ OOP thông qua cả mô hình **hàm tạo (constructor function)** và **class (ES6+).**

---

## 1. Các khái niệm cơ bản trong OOP

### **1.1. Object (Đối tượng)**
Đối tượng là thực thể chứa dữ liệu (properties) và các hành vi (methods).

Ví dụ về một object trong JavaScript:
```javascript
const person = {
  name: "John",
  age: 30,
  greet: function () {
    console.log(`Hello, my name is ${this.name}`);
  }
};

person.greet(); // Output: Hello, my name is John
```
Ở đây, `name` và `age` là thuộc tính, còn `greet` là phương thức.

---

### **1.2. Class (Lớp)**
Lớp là một khuôn mẫu để tạo ra các đối tượng có cùng thuộc tính và phương thức.

Ví dụ về class trong JavaScript:
```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

const person1 = new Person("Alice", 25);
person1.greet(); // Output: Hello, my name is Alice
```

---

### **1.3. Constructor (Hàm khởi tạo)**
Hàm `constructor` là phương thức đặc biệt trong class được gọi khi tạo đối tượng mới bằng từ khóa `new`.

Ví dụ:
```javascript
class Car {
  constructor(brand, year) {
    this.brand = brand;
    this.year = year;
  }

  getInfo() {
    return `${this.brand} - ${this.year}`;
  }
}

const car1 = new Car("Toyota", 2022);
console.log(car1.getInfo()); // Output: Toyota - 2022
```

---

### **1.4. Encapsulation (Đóng gói)**
Đóng gói là việc ẩn đi dữ liệu bên trong đối tượng, chỉ cho phép truy cập thông qua các phương thức.

Ví dụ:
```javascript
class BankAccount {
  #balance; // Thuộc tính private

  constructor(owner, balance) {
    this.owner = owner;
    this.#balance = balance;
  }

  deposit(amount) {
    if (amount > 0) this.#balance += amount;
  }

  getBalance() {
    return this.#balance;
  }
}

const account = new BankAccount("John", 1000);
account.deposit(500);
console.log(account.getBalance()); // Output: 1500
```
Dấu `#` trước `balance` giúp biến này chỉ có thể truy cập trong class.

---

### **1.5. Inheritance (Kế thừa)**
Kế thừa giúp một class có thể sử dụng lại các thuộc tính và phương thức từ class khác.

Ví dụ:
```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} makes a sound.`);
  }
}

class Dog extends Animal {
  speak() {
    console.log(`${this.name} barks.`);
  }
}

const dog = new Dog("Buddy");
dog.speak(); // Output: Buddy barks.
```
Class `Dog` kế thừa `Animal` nhưng có thể ghi đè phương thức `speak()`.

---

### **1.6. Polymorphism (Đa hình)**
Đa hình cho phép phương thức trong lớp con thay đổi cách hoạt động so với lớp cha.

Ví dụ:
```javascript
class Shape {
  area() {
    return 0;
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }

  area() {
    return Math.PI * this.radius ** 2;
  }
}

const circle = new Circle(5);
console.log(circle.area()); // Output: 78.53981633974483
```
Lớp `Circle` định nghĩa lại phương thức `area()` khác với lớp `Shape`.

---

### **1.7. Abstraction (Trừu tượng hóa)**
Trừu tượng hóa giúp ẩn đi chi tiết không cần thiết và chỉ hiển thị những gì quan trọng.

Ví dụ:
```javascript
class Vehicle {
  constructor(name, speed) {
    if (this.constructor === Vehicle) {
      throw new Error("Cannot instantiate abstract class");
    }
    this.name = name;
    this.speed = speed;
  }

  move() {
    console.log(`${this.name} is moving at ${this.speed} km/h.`);
  }
}

class Bike extends Vehicle {
  move() {
    console.log(`${this.name} is riding at ${this.speed} km/h.`);
  }
}

// const vehicle = new Vehicle("Generic", 50); // Lỗi
const bike = new Bike("Yamaha", 80);
bike.move(); // Output: Yamaha is riding at 80 km/h.
```
Lớp `Vehicle` là lớp trừu tượng, không thể tạo thể hiện trực tiếp.

---

## 2. Từ khóa `this` trong OOP JavaScript
- `this` tham chiếu đến đối tượng đang gọi phương thức.
- Trong class, `this` đại diện cho instance của class.

Ví dụ:
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
}

const person = new Person("Alice");
person.greet(); // Output: Hello, I'm Alice
```

---

## 3. Static Method và Property
- **Static method** là phương thức thuộc về class thay vì instance.
- **Static property** là thuộc tính thuộc về class thay vì instance.

Ví dụ:
```javascript
class MathUtil {
  static PI = 3.1415;

  static square(num) {
    return num * num;
  }
}

console.log(MathUtil.PI); // Output: 3.1415
console.log(MathUtil.square(5)); // Output: 25
```

---

## 4. Getters và Setters
Dùng để truy xuất hoặc cập nhật thuộc tính một cách an toàn.

Ví dụ:
```javascript
class Product {
  constructor(name, price) {
    this.name = name;
    this._price = price;
  }

  get price() {
    return this._price;
  }

  set price(value) {
    if (value < 0) throw new Error("Price cannot be negative");
    this._price = value;
  }
}

const product = new Product("Laptop", 1000);
console.log(product.price); // Output: 1000
product.price = 1200;
console.log(product.price); // Output: 1200
```

---

## 5. OOP JavaScript với Prototype (Cũ hơn)
Trước ES6, JavaScript sử dụng prototype để mô phỏng OOP.

Ví dụ:
```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.greet = function () {
  console.log(`Hello, my name is ${this.name}`);
};

const person1 = new Person("Bob", 28);
person1.greet(); // Output: Hello, my name is Bob
```
Cách này ít được dùng sau khi có `class` trong ES6.

---

## Kết luận
- **JavaScript hỗ trợ OOP** thông qua **Object, Class, Constructor, Inheritance, Polymorphism, Encapsulation, Abstraction**.
- **Class (ES6) là cách hiện đại** để sử dụng OOP trong JavaScript.
- **Prototype (ES5 trở về trước)** là cách tiếp cận cũ nhưng vẫn quan trọng.

Nếu bạn muốn áp dụng OOP vào dự án thực tế, hãy sử dụng `class` thay vì `prototype`. 🚀

---

## 4 tính chất

Trong lập trình hướng đối tượng (OOP), có **bốn tính chất chính** giúp tổ chức và quản lý code một cách hiệu quả:

1. **Encapsulation (Đóng gói)**
2. **Inheritance (Kế thừa)**
3. **Polymorphism (Đa hình)**
4. **Abstraction (Trừu tượng hóa)**

---

## **1. Encapsulation (Đóng gói)**

**Định nghĩa**:  
Đóng gói là kỹ thuật che giấu dữ liệu (hạn chế quyền truy cập trực tiếp vào thuộc tính của đối tượng) và chỉ cho phép truy cập thông qua các phương thức được định nghĩa.

### 🔹 **Ví dụ không đóng gói (truy cập trực tiếp vào thuộc tính)**

```javascript
class Person {
	constructor(name, age) {
		this.name = name;
		this.age = age;
	}
}

const person = new Person("Alice", 25);
console.log(person.age); // Output: 25 (Có thể truy cập trực tiếp)
```

➡️ Ở đây, `age` có thể bị thay đổi mà không có sự kiểm soát.

---

### 🔹 **Cách đóng gói trong JavaScript**

Trong JavaScript, có thể dùng dấu `#` để khai báo **private properties** (thuộc tính riêng tư):

```javascript
class BankAccount {
	#balance; // Biến private

	constructor(owner, balance) {
		this.owner = owner;
		this.#balance = balance;
	}

	deposit(amount) {
		if (amount > 0) this.#balance += amount;
	}

	getBalance() {
		return this.#balance;
	}
}

const account = new BankAccount("John", 1000);
account.deposit(500);
console.log(account.getBalance()); // Output: 1500
console.log(account.#balance); // ❌ Lỗi: Private field không thể truy cập từ bên ngoài
```

💡 **Lợi ích**:  
✔️ Ngăn chặn việc truy cập và thay đổi dữ liệu không hợp lệ.  
✔️ Tăng tính bảo mật và kiểm soát dữ liệu tốt hơn.

---

## **2. Inheritance (Kế thừa)**

**Định nghĩa**:  
Kế thừa cho phép một lớp con (child class) sử dụng lại thuộc tính và phương thức của lớp cha (parent class) mà không cần viết lại code.

### 🔹 **Ví dụ về kế thừa trong JavaScript**

```javascript
class Animal {
	constructor(name) {
		this.name = name;
	}

	speak() {
		console.log(`${this.name} makes a sound.`);
	}
}

class Dog extends Animal {
	speak() {
		console.log(`${this.name} barks.`);
	}
}

const dog = new Dog("Buddy");
dog.speak(); // Output: Buddy barks.
```

💡 **Lợi ích**:  
✔️ Giúp tái sử dụng code, giảm trùng lặp.  
✔️ Dễ dàng mở rộng chương trình.

---

## **3. Polymorphism (Đa hình)**

**Định nghĩa**:  
Đa hình cho phép các lớp con **ghi đè (override)** phương thức của lớp cha theo cách riêng của chúng.

### 🔹 **Ví dụ về đa hình**

```javascript
class Shape {
	area() {
		return 0;
	}
}

class Circle extends Shape {
	constructor(radius) {
		super();
		this.radius = radius;
	}

	area() {
		return Math.PI * this.radius ** 2;
	}
}

class Rectangle extends Shape {
	constructor(width, height) {
		super();
		this.width = width;
		this.height = height;
	}

	area() {
		return this.width * this.height;
	}
}

const circle = new Circle(5);
console.log(circle.area()); // Output: 78.53981633974483

const rectangle = new Rectangle(4, 5);
console.log(rectangle.area()); // Output: 20
```

💡 **Lợi ích**:  
✔️ Cho phép mở rộng chức năng mà không cần thay đổi mã nguồn lớp cha.  
✔️ Tăng tính linh hoạt khi làm việc với các đối tượng khác nhau.

---

## **4. Abstraction (Trừu tượng hóa)**

**Định nghĩa**:  
Trừu tượng hóa là ẩn đi các chi tiết phức tạp bên trong một class và chỉ cung cấp các phương thức quan trọng để tương tác với đối tượng.

### 🔹 **Ví dụ về trừu tượng hóa**

Trong JavaScript, có thể tạo lớp trừu tượng bằng cách **không cho phép khởi tạo trực tiếp**:

```javascript
class Vehicle {
	constructor(name, speed) {
		if (this.constructor === Vehicle) {
			throw new Error("Cannot instantiate abstract class");
		}
		this.name = name;
		this.speed = speed;
	}

	move() {
		console.log(`${this.name} is moving at ${this.speed} km/h.`);
	}
}

class Car extends Vehicle {
	move() {
		console.log(`${this.name} is driving at ${this.speed} km/h.`);
	}
}

const car = new Car("Toyota", 120);
car.move(); // Output: Toyota is driving at 120 km/h.
```

💡 **Lợi ích**:  
✔️ Giảm sự phức tạp bằng cách chỉ hiển thị những gì cần thiết.  
✔️ Giúp định nghĩa giao diện chung cho các lớp con.

---

## **Tóm tắt các tính chất trong OOP**

| **Tính chất**                    | **Mô tả**                                                                                              |
| -------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Encapsulation (Đóng gói)**     | Hạn chế truy cập trực tiếp vào dữ liệu bên trong đối tượng, chỉ có thể truy cập thông qua phương thức. |
| **Inheritance (Kế thừa)**        | Cho phép một class kế thừa thuộc tính và phương thức của class khác để tái sử dụng code.               |
| **Polymorphism (Đa hình)**       | Một phương thức có thể có các cách triển khai khác nhau ở các lớp con.                                 |
| **Abstraction (Trừu tượng hóa)** | Ẩn đi chi tiết không quan trọng và chỉ hiển thị các phương thức cần thiết.                             |

---

## **Kết luận**

- OOP giúp tổ chức code dễ quản lý, bảo trì và mở rộng hơn.
- JavaScript hỗ trợ OOP thông qua **class, constructor, prototype, và closure.**
- Bốn tính chất chính của OOP (**Encapsulation, Inheritance, Polymorphism, Abstraction**) giúp lập trình hiệu quả hơn.

👉 **Ứng dụng OOP trong thực tế:**  
✔️ Xây dựng hệ thống quản lý tài khoản ngân hàng.  
✔️ Quản lý danh sách sản phẩm trong cửa hàng.  
✔️ Phát triển game với các đối tượng như nhân vật, quái vật, vật phẩm, v.v.

---

## Ví dụ (TypeScript)

```ts
type TCar = {
	brand: string;
	run: () => void;
};

type TLawnMower = {
	brand: string;
	cut: () => void;
};

class Engine {
	constructor(public engine: string) {
		this.engine = engine;
	}

	start() {
		console.log(`Starting ${this.engine} engine...`);
	}
}

class Car extends Engine implements TCar {
	constructor(
		public readonly brand: string,
		public readonly engine: string,
	) {
		super(engine);
		this.brand = brand;
	}

	run() {
		console.log(`${this.brand} is running with ${this.engine} engine.`);
	}
}

class LawnMower extends Engine implements TLawnMower {
	constructor(
		public readonly brand: string,
		public readonly engine: string,
	) {
		super(engine);
		this.brand = brand;
	}

	cut() {
		console.log(`${this.brand} is cutting grass with ${this.engine} engine.`);
	}
}

// Example usage:
const myCar = new Car("Toyota", "V6");
myCar.start();
myCar.run();

const myLawnMower = new LawnMower("Honda", "4-stroke");
myLawnMower.start();
myLawnMower.cut();
```
