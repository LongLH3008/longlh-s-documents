# ES6: Arrow Function

## Arrow Function

ARROW FUNCTION
Arrow functions (hàm mũi tên) là một tính năng quan trọng được giới thiệu trong 
ECMAScript 2015 (ES6) để định nghĩa các hàm trong JavaScript một cách ngắn gọn và dễ đọc hơn. 
Arrow functions cung cấp cú pháp viết tắt cho việc định nghĩa hàm và có cách thức hoạt động 
khác so với hàm thông thường.

-  thường được sử dụng trong các tình huống đơn giản khi bạn cần một hàm ngắn gọn. 
Tuy nhiên, hãy lưu ý rằng chúng không phải lúc nào cũng là lựa chọn tốt, đặc biệt 
là khi bạn cần sử dụng this với ngữ cảnh của hàm mới. Trong trường hợp đó, bạn nên 
sử dụng hàm thông thường.

Cú pháp cơ bản:
Để tạo một arrow function, bạn sử dụng dấu mũi tên => sau danh sách tham số của hàm.
Ví dụ:

```js
    // Hàm thông thường
    function add(x, y) {
        return x + y;
    }
  
    // Arrow function
    const add = (x, y) => x + y;

// ------------------------------------------------------------------------------------------------------
```

Chỉ có một dòng trả về:
Nếu arrow function chỉ có một dòng trả về, bạn có thể bỏ đi dấu ngoặc nhọn {} và từ khóa return.
Ví dụ:

```js
const double = (x) => x * 2; // Arrow function ngắn gọn

// ------------------------------------------------------------------------------------------------------
```

Không thay đổi ngữ cảnh (lexical context):
Arrow functions không tạo ra một ngữ cảnh riêng biệt (context) cho this. 
Chúng sử dụng ngữ cảnh của hàm bên ngoài, điều này có thể giúp tránh các 
vấn đề về phạm vi của this mà bạn thường gặp khi sử dụng hàm thông thường.
Ví dụ:

```js
function Counter() {
    this.count = 0;
    setInterval(() => {
      this.count++;
      console.log(this.count);
    }, 1000);
  }
  
const counter = new Counter();

// ------------------------------------------------------------------------------------------------------
```

Không thể sử dụng arguments:
Arrow functions không có biến arguments. Nếu bạn cần truy cập các tham số của hàm, 
bạn nên sử dụng hàm thông thường hoặc rest parameters.
Ví dụ:

```js
const func = () => {
    console.log(arguments); // Lỗi: arguments is not defined
};
```

---

## Bài tập

Thực hành sử dụng Arrow Function
Sử dụng cú pháp arrow function viết hàm sum nhận vào 2 tham số a và b và trả về tổng của chúng.

```js
var sum = (a,b) => a+b;

// Expected results
console.log(sum(1, 2)) // Output: 3
console.log(sum(2, 2)) // Output: 4
```
