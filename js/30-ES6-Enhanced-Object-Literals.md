# ES6: Enhanced Object Literals

[ ENHANCED OBJECT LITERALS ]

Enhanced object literals là một tính năng trong ECMAScript 2015 (ES6) giúp bạn tạo và cấu hình 
đối tượng một cách dễ dàng và nhanh chóng bằng cách cung cấp cú pháp cải tiến và các khả năng 
mở rộng so với cách truyền thống.

Enhanced object literals giúp bạn viết mã nguồn ngắn gọn và dễ đọc hơn khi tạo và cấu hình các 
đối tượng trong JavaScript. Đây là một phần của nhiều cải tiến của ES6 nhằm nâng cao sức mạnh 
và sự linh hoạt của ngôn ngữ.

[ Khiển trực tiếp tên thuộc tính ]
Bạn có thể trực tiếp sử dụng biểu thức làm tên thuộc tính của đối tượng mà không cần khai báo tên thuộc tính bằng cú pháp key: value.
Ví dụ:

```js
const propName = 'age';

const person = {
  name: 'John',
  [propName]: 30,
};

console.log(person); // Kết quả: { name: 'John', age: 30 }

// ---------------------------------------------------------------------------------------------------------------
```

[ Định nghĩa phương thức ngắn gọn ]
Bạn có thể định nghĩa phương thức trong đối tượng mà không cần sử dụng cú pháp function.
Ví dụ:

```js
const calculator = {
    add(x, y) {
      return x + y;
    },
    subtract(x, y) {
      return x - y;
    },
  };
  
  console.log(calculator.add(5, 3)); // Kết quả: 8
  console.log(calculator.subtract(5, 3)); // Kết quả: 2

// ---------------------------------------------------------------------------------------------------------------
```

[ Định nghĩa getter và setter ]
Bạn có thể sử dụng cú pháp get và set để định nghĩa getter và setter cho thuộc tính của đối tượng.
Ví dụ:

```js
const temperature = {
    _celsius: 0,
    get celsius() {
      return this._celsius;
    },
    set celsius(value) {
      this._celsius = value;
    },
    get fahrenheit() {
      return (this._celsius * 9/5) + 32;
    },
    set fahrenheit(value) {
      this._celsius = (value - 32) * 5/9;
    },
  };
  
  temperature.celsius = 25;
  console.log(`Celsius: ${temperature.celsius}°C, Fahrenheit: ${temperature.fahrenheit}°F`);
  // Kết quả: Celsius: 25°C, Fahrenheit: 77°F
```
