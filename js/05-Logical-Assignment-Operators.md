# Logical Assignment Operators

Logical Assignment Operators là một tính năng JavaScript mới giới thiệu trong ECMAScript 2022 (ES13). Đây là sự kết hợp của toán tử logic và toán tử gán để thực hiện cả hai thao tác này trong một lần. Cụ thể, có hai toán tử logical assignment là `&&=` và `||=`.

1. **Logical AND Assignment (`&&=`):**
   - Dùng để thực hiện phép toán AND logic và gán giá trị mới vào biến nếu điều kiện là true.
   - Ví dụ:

     ```javascript
     let x = true;
     x &&= false; // Nếu x là true, thì x sẽ được gán giá trị false
     console.log(x); // Kết quả: false
     ```

2. **Logical OR Assignment (`||=`):**
   - Dùng để thực hiện phép toán OR logic và gán giá trị mới vào biến nếu điều kiện là false.
   - Ví dụ:

     ```javascript
     let y = false;
     y ||= true; // Nếu y là false, thì y sẽ được gán giá trị true
     console.log(y); // Kết quả: true
     ```

Logical Assignment Operators giúp rút ngắn cú pháp và làm code trở nên ngắn gọn hơn, đặc biệt khi muốn thực hiện một phép toán logic và gán giá trị mới trong một dòng code. Tính năng này thường được sử dụng trong các biểu thức điều kiện ngắn gọn. Tuy nhiên, cần lưu ý rằng chúng chỉ là một phần của phiên bản mới nhất của JavaScript và có thể không được hỗ trợ trên tất cả các trình duyệt và môi trường JavaScript.
