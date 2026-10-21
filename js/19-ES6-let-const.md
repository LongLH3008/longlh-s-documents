# ES6: let & const

LET | CONST
let và const là hai cách mới để khai báo biến trong JavaScript
chúng có một số sự khác biệt quan trọng so với cách truyền thống sử dụng var. 

1. **let**:
- let được sử dụng để khai báo biến với phạm vi cục bộ (block scope). 
Điều này có nghĩa là biến chỉ có thể truy cập được trong phạm vi nơi nó được khai báo, 
chẳng hạn như trong một khối {}.

- Bạn có thể tái khai báo một biến let trong cùng một phạm vi.
- Biến let không được khởi tạo tự động, vì vậy chúng có giá trị undefined cho đến khi 
bạn gán giá trị cho chúng.

2. **const**:
- const cũng được sử dụng để khai báo biến với phạm vi cục bộ.
- Tuy nhiên, biến const phải được gán giá trị ban đầu và sau đó không thể thay đổi giá trị của nó.
- Giá trị của biến const phải là không thay đổi sau khi được gán.

Sử dụng let và const giúp giảm khả năng xảy ra các lỗi logic trong mã JavaScript và tạo ra mã 
dễ đọc và dễ bảo trì hơn. let thường được sử dụng cho các biến có thể thay đổi giá trị trong quá 
trình chạy, trong khi const thường được sử dụng cho các biến có giá trị không thay đổi sau khi khởi tạo.

```js
// Ví dụ sử dụng let:
if (true) {
  let x = 10;
  console.log(x); // 10
}

console.log(x); // Error: x is not defined


// Ví dụ sử dụng const:
const pi = 3.14159;
pi = 3; // Error: Assignment to constant variable
```
