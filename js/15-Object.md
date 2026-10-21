# Object

## Object

OBJECT:

```js
var myInfo = {
    name: 'Long',
    age: 23,
    add: 'Hanoi'
};

// Thêm 1 key vào object (key: email)
myInfo.email = 'longlhph31572@fpt.edu.vn'

// Xóa 1 key từ object
delete myInfo.age

// Lấy key-value từ object
console.log(myInfo.name);

// Đưa 1 key vào object từ 1 biến ngoài
var emailKey = "";
var myInfo2 = {
    name: 'Long',
    age: 23,
    add: 'Hanoi',
    [emailKey]: 'longlhph31572@fpt.edu.vn'
};

// Gọi 1 key là function trong object
var myInfo3 = {
    name: 'Long',
    age: 23,
    add: 'Hanoi',
    getName: function () {
        return this.name;
        // this.name = myInfo3.name
    }
};
console.log(myInfo3.getName());
```

---

## Object Methods

### 1. **`Object.keys(obj)`:**
   - Trả về một mảng chứa tất cả các key (khóa) của đối tượng.
   ```javascript
   const person = { name: 'John', age: 25, city: 'New York' };
   const keys = Object.keys(person);
   console.log(keys); // Output: ['name', 'age', 'city']
   ```

### 2. **`Object.values(obj)`:**
   - Trả về một mảng chứa tất cả các giá trị của đối tượng.
   ```javascript
   const person = { name: 'John', age: 25, city: 'New York' };
   const values = Object.values(person);
   console.log(values); // Output: ['John', 25, 'New York']
   ```

### 3. **`Object.entries(obj)`:**
   - Trả về một mảng các cặp key-value của đối tượng dưới dạng mảng 2 phần tử.
   ```javascript
   const person = { name: 'John', age: 25, city: 'New York' };
   const entries = Object.entries(person);
   console.log(entries);
   // Output: [['name', 'John'], ['age', 25], ['city', 'New York']]
   ```

### 4. **`Object.assign(target, ...sources)`:**
   - Sao chép các giá trị từ một hoặc nhiều đối tượng nguồn vào đối tượng đích.
   ```javascript
   const person = { name: 'John', age: 25 };
   const details = { city: 'New York', job: 'Engineer' };
   const mergedObject = Object.assign({}, person, details);
   console.log(mergedObject);
   // Output: { name: 'John', age: 25, city: 'New York', job: 'Engineer' }
   ```

### 5. **`Object.hasOwnProperty(key)`:**
   - Kiểm tra xem đối tượng có chứa key (khóa) hay không.
   ```javascript
   const person = { name: 'John', age: 25 };
   console.log(person.hasOwnProperty('name')); // Output: true
   console.log(person.hasOwnProperty('city')); // Output: false
   ```

### 6. **`Object.freeze(obj)`:**
   - Đóng băng đối tượng, ngăn chặn sự thay đổi giá trị của các thuộc tính.
   ```javascript
   const person = { name: 'John', age: 25 };
   Object.freeze(person);
   person.age = 30; // Thay đổi sẽ bị bỏ qua (trong chế độ strict mode).
   console.log(person); // Output: { name: 'John', age: 25 }
   ```
Dưới đây là một số phương thức khác để làm việc với đối tượng trong JavaScript:

### 7. **`Object.create(proto[, propertiesObject])`:**
   - Tạo một đối tượng mới với đối tượng prototype được chỉ định và các thuộc tính được cung cấp.
   ```javascript
   const personPrototype = { greet: function() { console.log('Hello!'); } };
   const john = Object.create(personPrototype);
   john.name = 'John';
   john.greet(); // Output: Hello!
   ```

### 8. **`Object.defineProperty(obj, prop, descriptor)`:**
   - Thêm hoặc cập nhật một thuộc tính trên đối tượng với một mô tả thuộc tính.
   ```javascript
   const person = {};
   Object.defineProperty(person, 'age', { value: 25, writable: false });
   console.log(person.age); // Output: 25
   person.age = 30; // Thay đổi sẽ bị bỏ qua (do writable: false)
   console.log(person.age); // Output: 25
   ```

### 9. **`Object.getOwnPropertyNames(obj)`:**
   - Trả về một mảng các tên thuộc tính (bao gồm cả không liên tục).
   ```javascript
   const person = { name: 'John', age: 25 };
   const propertyNames = Object.getOwnPropertyNames(person);
   console.log(propertyNames); // Output: ['name', 'age']
   ```

### 10. **`Object.getPrototypeOf(obj)`:**
   - Trả về prototype của đối tượng.
   ```javascript
   const person = { name: 'John', age: 25 };
   const prototype = Object.getPrototypeOf(person);
   console.log(prototype); // Output: [Object: null prototype] {}
   ```

### 11. **`Object.is(obj1, obj2)`:**
   - So sánh giá trị của hai đối tượng.
   ```javascript
   const obj1 = { key: 'value' };
   const obj2 = { key: 'value' };
   console.log(Object.is(obj1, obj2)); // Output: false
   ```

### 12. **`Object.keys(obj)`:**
   - Trả về một mảng chứa tất cả các key (khóa) có thể liệt kê của đối tượng.
   ```javascript
   const person = { name: 'John', age: 25 };
   const keys = Object.keys(person);
   console.log(keys); // Output: ['name', 'age']
   ```

### 13. **`Object.seal(obj)`:**
   - "Niêm phong" đối tượng, ngăn chặn thêm hoặc xóa bất kỳ thuộc tính nào.
   ```javascript
   const person = { name: 'John', age: 25 };
   Object.seal(person);
   person.job = 'Engineer'; // Thêm thuộc tính mới sẽ bị bỏ qua.
   delete person.age; // Xóa thuộc tính sẽ bị bỏ qua.
   console.log(person); // Output: { name: 'John', age: 25 }
   ```

---

## Object Constructor

OBJECT CONSTRUCTOR

```js
// Thiết kế dối tượng / constructor ( hàm tạo )
var User = function(firstName, lastName, avatar) {
    this.firstName = firstName
    this.lastName = lastName
    this.avatar = avatar

    this.getName = function () {
        return `${this.firstName} ${this.lastName}`;
    }
};

// Tạo đối tượng với các tham số
var author = new User('Long', 'Lương', 'Avatar');
var user = new User('Hà', 'Trần', 'Avatar');

// Thêm thuộc tính vào đói tượng
author.title = 'Chia sẻ kiến thức';
user.comment = 'Rât hay và bổ ích';

console.log(author);
console.log(user);
```

---

## Object Prototype

OBJECT PROTOTYPE

```js
// Thiết kế dối tượng / constructor ( hàm tạo )
var User = function(firstName, lastName, avatar) {
    this.firstName = firstName
    this.lastName = lastName
    this.avatar = avatar

    this.getName = function () {
        return `${this.firstName} ${this.lastName}`;
    }
};

// Thêm thuộc tính vào hàm tạo
User.prototype.className = 'FPT'
// Chuyển className vừa tạo thành thuộc tính this để có thể kế thừa và tái sử dụng
User.prototype.getClassName = function() {
    return this.className;
}

// Tạo đối tượng với các tham số
var author = new User('Long', 'Lương', 'Avatar');
var user = new User('Hà', 'Trần', 'Avatar');

console.log(author);
console.log(user);
```

---

## Bài tập

Tạo object constructor Animal

Hãy tạo object constructor cho nhóm Animal có 3 thuộc tính là name, leg và speed.
Trong đó, name là để lưu tên động vật, leg là số lượng chân và speed là tốc độ di 
chuyển của động vật đó.

Sau đó, hãy tạo một đối tượng từ object constructor trên để mô tả 1 con vẹt, lưu
đối tượng vào biến parrot.

```js
var Animal = function (name, leg, speed) {
    this.name = name,
    this.leg = leg,
    this.speed = speed
};

var parrot = new Animal('Dog', 4, '23km/h');

// ---------------------------------------------------------------------------------------------------
```

Thêm phương thức getFullName

Để vượt qua thử thách này, bạn hãy tạo một object constructor Student gồm:
firstName, lastName. Sau đó, định nghĩa thêm phương thức là getFullName
phương thức này sẽ trả về tên đầy đủ của sinh viên.

Giữa firstName và lastName cần có 1 khoảng trắng (1 dấu space).

```js
var Student = function(firstName, lastName) {
    this.firstName = firstName,
    this.lastName = lastName
};

Student.prototype.getFullName = function () {
    return this.firstName + ' ' + this.lastName;
}

// Ví dụ khi sử dụng
var student = new Student('Long', 'Bui');

console.log(student.firstName);  // 'Long'
console.log(student.lastName);  // 'Bui'
console.log(student.getFullName());  // 'Long Bui'

// ---------------------------------------------------------------------------------------------------
```

Hoàn thiện hàm getNextYear

Để vượt qua thử thách này, bạn hãy tạo hàm getNextYear, hàm này sẽ trả về
năm kế tiếp. Ví dụ, năm nay là 2022, hàm sẽ trả về 2023 là kiểu số.

```js
function getNextYear () {
    let date = new Date();
    return date.getFullYear() + 1;
}

// ---------------------------------------------------------------------------------------------------
```

Lấy ngẫu nhiên phần tử của mảng

Hãy tạo hàm getRandomItem, hàm này nhận 1 tham số là mảng và sẽ trả về
ngẫu nhiên 1 phần tử của mảng.

```js
function getRandomItem (array) {
    // Math.floor() : làm tròn dưới
    // Math.random() * array.legnth: ngẫu nhiên trong khoảng array.length
    return array[Math.floor(Math.random() * array.length)];
}
```
