# ES6: Classes

## Classes

CLASSES
là một tính năng mới giúp bạn định nghĩa các đối tượng và tạo các đối tượng từ lớp đó. 
Lớp được xem như một bản thiết kế (template) để tạo các đối tượng có cùng cấu trúc và hành vi. 
Classes cung cấp cách định nghĩa đối tượng hướng đối tượng một cách rõ ràng và dễ đọc hơn

[ Cú pháp định nghĩa lớp ]:
Để định nghĩa một lớp, bạn sử dụng từ khóa class, sau đó là tên lớp.
Ví dụ:

```js
class Animal {
    constructor(name) {
      this.name = name;
    }
}

// --------------------------------------------------------------------------------------------------------
```

[ Phương thức constructor ]:
Một phương thức đặc biệt gọi là constructor được sử dụng để khởi tạo đối tượng khi bạn tạo một thể hiện (instance) của lớp.
Trong constructor, bạn có thể thiết lập các thuộc tính ban đầu cho đối tượng.
Ví dụ:

```js
class Animal {
    constructor(name) {
      this.name = name;
    }
  }
  const dog = new Animal('Buddy');
  console.log(dog.name); // Kết quả: "Buddy"

// --------------------------------------------------------------------------------------------------------
```

[ Phương thức của lớp ]:
Bạn có thể thêm các phương thức vào lớp bằng cách định nghĩa chúng bên trong lớp.
Ví dụ:

```js
class Animal_2 {
    constructor(name) {
      this.name = name;
    }
  
    makeSound() {
      console.log('Animal makes a sound');
    }
  }
  const dog2 = new Animal_2('Buddy');
  dog2.makeSound(); // Kết quả: "Animal makes a sound"

// --------------------------------------------------------------------------------------------------------
```

[ Kế thừa (Inheritance) ]
Bạn có thể tạo lớp con (subclass) dựa trên một lớp cha (superclass) đã tồn tại bằng cách sử dụng từ khóa extends.
Ví dụ:

```js
class Cat extends Animal {
    constructor(name, breed) {
      super(name); // Gọi constructor của lớp cha
      this.breed = breed;
    }
  
    meow() {
      console.log(`${this.name} the ${this.breed} meows`);
    }
  }
  const fluffy = new Cat('Fluffy', 'Persian');
  fluffy.meow(); // Kết quả: "Fluffy the Persian meows"

// --------------------------------------------------------------------------------------------------------
```

[ Tính đóng gói và bảo vệ ]
Classes cho phép bạn quản lý tính đóng gói và bảo vệ thông tin. Thuộc tính và phương thức có thể được định nghĩa là public, private hoặc protected.
Ví dụ:

```js
class Person {
    #ssn; // Thuộc tính private
  
    constructor(name, ssn) {
      this.name = name;
      this.#ssn = ssn; // Sử dụng thuộc tính private
    }
  
    getSSN() {
      return this.#ssn; // Truy cập thuộc tính private qua phương thức
    }
  }
```

---

## Bài tập

Thực hành sử dụng Class

Tạo class Person có thuộc tính name và age

Chú ý: Không dùng constructor function nhé.

```js
class Person {
    constructor(name, age){
        this.name = name
        this.age = age
    }
}

// Expected results
const person = new Person('Long', 22);
console.log(`Tên: ${person.name}, tuổi: ${person.age}`);
// Output: 'Tên: Long, tuổi: 22'
```
