# ES6: Default Parameter

Default parameters (tham số mặc định)
  là một tính năng trong ECMAScript 2015 (ES6) cho phép bạn định nghĩa giá trị mặc định cho các 
  tham số của hàm trong trường hợp không có giá trị được truyền vào cho tham số đó khi hàm được gọi. 
  Điều này làm cho mã nguồn dễ đọc hơn và giúp tránh các lỗi khi một tham số bị thiếu.

  Default parameters cho phép bạn xử lý các trường hợp khi không có giá trị được truyền vào tham số một 
  cách dễ dàng và an toàn, giúp giảm thiểu sự xuất hiện của giá trị undefined và tạo mã nguồn dễ đọc hơn.

Một số điểm quan trọng về default parameters:
    + Bạn có thể sử dụng default parameters cho bất kỳ tham số nào của hàm, không chỉ là tham số cuối cùng.
    
    + Default parameters hoạt động với bất kỳ kiểu dữ liệu nào, không chỉ là chuỗi (string)

    + Nếu bạn không đặt giá trị mặc định cho một tham số và hàm được gọi mà không có giá trị truyền vào cho 
    tham số đó, thì tham số đó sẽ có giá trị undefined.

    + Default parameters được tính toán từ trái sang phải. Điều này có nghĩa là nếu bạn muốn sử dụng giá trị 
    của một tham số trong default value của tham số khác, bạn phải đặt tham số đó ở bên trước.

[ Cú pháp định nghĩa tham số mặc định ]

```js
function functionName(param1 = defaultValue1, param2 = defaultValue2) {
    // Hàm xử lý
}

// -------------------------------------------------------------------------------------------------------

// [ Sử dụng giá trị mặc định cho tham số đối tượng ]
function greet(person = { name: 'Guest', age: 30 }) {
    console.log(`Hello, ${person.name}! You are ${person.age} years old.`);
  }
  
  greet(); // Kết quả: "Hello, Guest! You are 30 years old."
  greet({ name: 'John', age: 25 }); // Kết quả: "Hello, John! You are 25 years old."

// -------------------------------------------------------------------------------------------------------

// [ Sử dụng giá trị mặc định cho tham số số học ]
function multiply(a, b = 2) {
    return a * b;
  }
  
  console.log(multiply(5)); // Kết quả: 10, vì b lấy giá trị mặc định là 2
  console.log(multiply(5, 3)); // Kết quả: 15, vì b được truyền giá trị 3

// -------------------------------------------------------------------------------------------------------

// [ Kết hợp default parameters với rest parameters ]
function printNumbers(prefix = 'Number:', ...numbers) {
    console.log(prefix);
    for (const number of numbers) {
      console.log(number);
    }
  }
  
  printNumbers('List of Numbers:', 1, 2, 3);
  // Kết quả:
  // "List of Numbers:"
  // 1
  // 2
  // 3
  
  printNumbers(); // Kết quả: "Number:"  

// -------------------------------------------------------------------------------------------------------

// [ Sử dụng default parameters trong hàm callback ]
function fetchData(url, options = {}) {
    const method = options.method || 'GET';
    const headers = options.headers || {};
  
    console.log(`Fetching data from ${url} using ${method} method with headers:`, headers);
  }
  
  fetchData('https://example.com/api/data');
  // Kết quả: "Fetching data from https://example.com/api/data using GET method with headers: {}"  

// -------------------------------------------------------------------------------------------------------

// [ Sử dụng giá trị mặc định cho tham số trong hàm arrow ]
const greet = (name = 'Guest') => `Hello, ${name}!`;

console.log(greet()); // Kết quả: "Hello, Guest!"
console.log(greet('John')); // Kết quả: "Hello, John!"
```
