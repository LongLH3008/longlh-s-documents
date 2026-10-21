# HOISTING

Hoisting là một tính chất trong JavaScript mà các khai báo biến và khai báo hàm có thể được "nâng lên" (hoist) lên đầu của phạm vi của chúng trước khi mã JavaScript được thực thi.

Trong quá trình hoisting, JavaScript di chuyển tất cả các khai báo biến và hàm lên đầu của phạm vi của chúng, bất kể chúng được khai báo ở đâu trong mã nguồn. Tuy nhiên, chỉ có phần khai báo được nâng lên (hoisted), còn phần gán giá trị thực sự không bị nâng lên.

Ví dụ:

```javascript
console.log(myVariable); // Kết quả: undefined
var myVariable = 10;
console.log(myVariable); // Kết quả: 10
```

Trong đoạn mã trên, mặc dù `myVariable` được sử dụng trước khi nó được gán giá trị, không có lỗi nào xảy ra. Điều này xảy ra vì trong quá trình hoisting, khai báo biến `var myVariable;` được nâng lên đầu của phạm vi, nhưng phần gán giá trị `myVariable = 10;` vẫn ở tại vị trí ban đầu.

### Ví dụ với Func ( hàm )

Để hiểu rõ hơn về hoisting, hãy xem xét một ví dụ với khai báo hàm:

```javascript
sayHello(); // Kết quả: "Hello, world!"

function sayHello() {
    console.log("Hello, world!");
}
```

Trong ví dụ này, hàm `sayHello` được gọi trước khi nó được định nghĩa trong mã nguồn. Tuy nhiên, không có lỗi nào xảy ra do tính chất hoisting. Trình biên dịch JavaScript nâng lên đầu của phạm vi hàm `sayHello()` trước khi mã được thực thi, cho phép gọi hàm trước khi nó được định nghĩa.

Lưu ý rằng hoisting chỉ diễn ra với khai báo biến (`var`, `let`, `const`) và khai báo hàm, không phải với phần gán giá trị thực sự. Điều này có thể dẫn đến hiểu lầm nếu bạn không hiểu rõ cách hoisting hoạt động.

Tất nhiên, hãy xem xét ví dụ với `const`, `let` và `var` để hiểu cách hoisting hoạt động với từng loại biến.

### Ví dụ với `var`

```javascript
console.log(myVar); // Kết quả: undefined
var myVar = 10;
console.log(myVar); // Kết quả: 10
```

Trong trường hợp này, khai báo `var myVar;` được nâng lên đầu của phạm vi, nhưng phần gán giá trị `myVar = 10;` vẫn ở tại vị trí ban đầu. 

### Ví dụ với `let` và `const`

```javascript
console.log(myLet); // Error: Cannot access 'myLet' before initialization
let myLet = 10;

console.log(myConst); // Error: Cannot access 'myConst' before initialization
const myConst = 20;
```

Trong trường hợp của `let` và `const`, JavaScript không cho phép truy cập biến trước khi nó được khai báo (không hoisted). Điều này gọi là "temporal dead zone" (vùng thời gian tạm thời bị chặn). Bất kỳ truy cập nào trước khi biến được khai báo sẽ dẫn đến lỗi.

Về cách hoisting hoạt động với `let` và `const`, chúng vẫn được nâng lên đầu của phạm vi của chúng, nhưng không thể truy cập trước khai báo do chúng ở trong "vùng thời gian tạm thời" (temporal dead zone) cho đến khi chúng được khai báo chính thức trong mã nguồn.
