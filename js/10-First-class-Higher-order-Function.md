# First-class & Higher-order Function

First-Class Functions và Higher-Order Functions không phải là cùng một khái niệm, nhưng thường được sử dụng cùng nhau và có mối quan hệ chặt chẽ.

**First-Class Functions:**
- Đây là một tính chất của ngôn ngữ lập trình.
- Functions được coi là first-class citizens, có thể được truyền như tham số, gán cho biến, và trả về từ một hàm khác.
- Ví dụ:

  ```javascript
  const greet = function() {
    return "Hello!";
  };

  const sayHello = greet; // Gán function cho biến
  const result = sayHello(); // Gọi function từ biến
  ```

**Higher-Order Functions:**
- Đây là một loại hàm (function).
- Là hàm nhận hoặc trả về một function.
- Ví dụ:

  ```javascript
  function multiplier(factor) {
    return function(x) {
      return x * factor;
    };
  }

  const double = multiplier(2); // Trả về function
  const result = double(5); // Gọi lại function
  ```

Khi nói về Higher-Order Functions trong ngữ cảnh của JavaScript, thường người ta liên kết chúng với việc sử dụng callback functions, nhưng cả hai khái niệm đều tồn tại độc lập và có thể được sử dụng mà không cần đến nhau.

Nói cách khác, first-class functions là một đặc điểm của ngôn ngữ, trong khi higher-order functions là một kiểu hàm có thể nhận và/hoặc trả về functions. Trong JavaScript, chúng thường được sử dụng cùng nhau để tạo ra mã linh hoạt và dễ đọc.
