# ES6: Template Literals

Template literals (còn được gọi là template strings) là một tính năng quan trọng được giới thiệu trong ECMAScript 2015 (ES6) cho phép bạn tạo chuỗi trong JavaScript một cách dễ đọc và linh hoạt hơn. Template literals cho phép bạn nhúng biểu thức JavaScript vào trong chuỗi bằng cách sử dụng cú pháp ${expression}. Dưới đây là một số điểm quan trọng về template literals:

+ Cú pháp cơ bản:
Bạn sử dụng dấu ngoặc grave accent (`) để định nghĩa một template literal.
Biểu thức JavaScript được nhúng bên trong ${} trong chuỗi.

```js
   
//    Ví dụ:
const name = 'John';
const greeting = `Hello, ${name}!`; // Sử dụng template literal
console.log(greeting); // Kết quả: "Hello, John!"

// Biểu thức bên trong:
// Bạn có thể nhúng bất kỳ biểu thức JavaScript hợp lệ nào vào bên trong ${}.
// Điều này bao gồm biến, toán tử, gọi hàm và thậm chí là biểu thức khác.

const num1 = 5;
const num2 = 10;
const sum = `The sum of ${num1} and ${num2} is ${num1 + num2}.`;
console.log(sum); // Kết quả: "The sum of 5 and 10 is 15."

// Multiline strings:
// Template literals cũng cho phép bạn tạo chuỗi đa dòng một cách dễ dàng mà 
// không cần sử dụng các ký tự thoát ra.

const multiline = `
  This is a multiline
  string created with
  template literals.
`;
console.log(multiline);
// Kết quả:
// "This is a multiline
//  string created with
//  template literals."
```
