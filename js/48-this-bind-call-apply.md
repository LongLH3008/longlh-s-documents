# this, bind, call, apply

## this

Từ khóa `this` trong JavaScript tham chiếu đến đối tượng hiện tại mà một phương thức được gọi, hoặc là một cách để truy cập thông tin của đối tượng mà nó đang hoạt động trong phạm vi hiện tại.

### Sử dụng `this` trong đối tượng:

```javascript
let person = {
    name: 'John',
    greet: function() {
        console.log(`Hello, my name is ${this.name}`);
    }
};

person.greet(); // Kết quả: "Hello, my name is John"
```

Trong ví dụ trên, `this.name` tham chiếu đến thuộc tính `name` của đối tượng mà phương thức `greet` đang hoạt động.

### Sử dụng `this` trong hàm xử lý sự kiện:

```javascript
<button onclick="showMessage()">Click me</button>

function showMessage() {
    console.log(this); // Kết quả: Tham chiếu đến đối tượng DOM của nút button được gọi
}
```

Trong trường hợp này, khi bạn sử dụng `this` trong một hàm xử lý sự kiện (ví dụ như `onclick` của một button), nó sẽ tham chiếu đến đối tượng DOM của phần tử mà sự kiện được kích hoạt.

### Sử dụng `this` trong hàm constructor:

```javascript
function Person(name) {
    this.name = name;
    this.greet = function() {
        console.log(`Hello, my name is ${this.name}`);
    };
}

let john = new Person('John');
john.greet(); // Kết quả: "Hello, my name is John"
```

Trong hàm constructor, `this` tham chiếu đến đối tượng được tạo ra bởi hàm constructor.

### Các trường hợp đặc biệt của `this`:

**Trong arrow function**: arrow function không có `this` của riêng nó, nó sẽ lấy giá trị của `this` từ phạm vi ngoài cùng mà nó được định nghĩa.

```js
    let obj = {
        value: 'Hello',
        getValue: function() {
            return () => {
                console.log(this.value);
            };
        },
    };

    let printValue = obj.getValue();
    printValue(); // Kết quả: "Hello"
```

`this` trong JavaScript rất linh hoạt và quan trọng để hiểu để làm việc với các phương thức, đối tượng và các ngữ cảnh khác nhau trong ngôn ngữ này.

---

## bind, call, apply

Các phương thức `bind`, `call`, và `apply` đều liên quan đến cách xử lý `this` trong JavaScript và được sử dụng để thay đổi ngữ cảnh của `this` trong hàm.

### 1. `bind`

Phương thức `bind` tạo ra một phiên bản mới của một hàm với giá trị `this` được ràng buộc với một đối tượng cụ thể.

**Cú pháp:**

```javascript
let newFunc = func.bind(thisArg[, arg1[, arg2[, ...]]]);
```

**Ví dụ:**

```javascript
let person = {
    name: 'John',
    greet: function() {
        console.log(`Hello, ${this.name}`);
    }
};

let boundFunc = person.greet.bind(person);

boundFunc(); // Kết quả: "Hello, John"
```
Trong ví dụ trên, `bind` tạo ra một bản sao của hàm `greet`, nơi giá trị `this` được ràng buộc với đối tượng `person`. Khi `boundFunc` được gọi, `this` bên trong nó được ràng buộc với `person`, vì vậy nó in ra "Hello, John".

Nếu không sử dụng `bind` và gọi trực tiếp phương thức `person.greet`, `this` trong hàm `greet` sẽ được xác định bởi ngữ cảnh gọi hàm. Trong JavaScript, ngữ cảnh của `this` sẽ phụ thuộc vào cách mà hàm được gọi.

Nếu gọi `person.greet()` trực tiếp:

```javascript
let person = {
    name: 'John',
    greet: function() {
        console.log(`Hello, ${this.name}`);
    }
};

person.greet(); // Kết quả: "Hello, John"
```

Trong trường hợp này, `this` sẽ trỏ đến đối tượng `person` bởi vì phương thức `greet` được gọi trực tiếp từ `person`.

Tuy nhiên, nếu gán hàm `greet` cho một biến khác và gọi nó từ biến đó, ngữ cảnh của `this` sẽ thay đổi:

```javascript
let person = {
    name: 'John',
    greet: function() {
        console.log(`Hello, ${this.name}`);
    }
};

let greetFunc = person.greet;
greetFunc(); // Kết quả: "Hello, undefined" hoặc lỗi (trong chế độ strict mode)
```

Trong trường hợp này, `this` không còn trỏ đến đối tượng `person` mà thay vào đó, nó trở thành một ngữ cảnh khác (global context trong non-strict mode hoặc undefined trong strict mode). Kết quả có thể là "Hello, undefined" hoặc sẽ xảy ra lỗi, tùy thuộc vào việc có sử dụng chế độ strict mode hay không.

### 2. `call`

Phương thức `call` được sử dụng để gọi một hàm và thiết lập giá trị của `this`, cùng với các tham số được truyền vào hàm.

**Cú pháp:**

```javascript
func.call(thisArg, arg1, arg2, ...);
```

**Ví dụ:**

```javascript
function greet(message) {
    console.log(`${message}, ${this.name}`);
}

let person = { name: 'Alice' };

greet.call(person, 'Hello'); // Kết quả: "Hello, Alice"
```

Trong ví dụ này, `call` được sử dụng để gọi hàm `greet` và truyền giá trị `this` là đối tượng `person`, cùng với tham số `message` là 'Hello'.

### 3. `apply`

`apply` tương tự như `call`, nhưng nhận vào một mảng các tham số thay vì danh sách tham số riêng lẻ.

**Cú pháp:**

```javascript
func.apply(thisArg, [argsArray]);
```

**Ví dụ:**

```javascript
function greet(message1, message2) {
    console.log(`${message1}, ${message2}, ${this.name}`);
}

let person = { name: 'Bob' };

greet.apply(person, ['Hi', 'How are you?']); // Kết quả: "Hi, How are you?, Bob"
```

Trong ví dụ trên, `apply` gọi hàm `greet` với `this` là `person`, và truyền vào một mảng các tham số 'Hi' và 'How are you?'.

Cả `call` và `apply` cho phép bạn gọi một hàm với một ngữ cảnh `this` cụ thể, điều này rất hữu ích khi bạn muốn tái sử dụng hàm và định nghĩa ngữ cảnh của `this` một cách rõ ràng.
