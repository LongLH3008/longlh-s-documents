# ES6: Modules

[ MODULES ]

Trong ECMAScript 2015 (ES6), modules là một tính năng mạnh mẽ giúp bạn tổ chức và quản lý mã nguồn 
JavaScript một cách dễ dàng và hiệu quả hơn. Modules cho phép bạn chia nhỏ mã nguồn thành các 
phần (modules) độc lập, mỗi phần thường tương ứng với một chức năng cụ thể trong ứng dụng của bạn. 
Điều này giúp giảm thiểu sự phức tạp, cải thiện tái sử dụng mã nguồn và quản lý phụ thuộc 
(dependencies) của ứng dụng.

Khái niệm cơ bản:
  Export (Xuất): Để xuất một biến, hàm hoặc lớp từ một module, bạn sử dụng từ khóa export.

  Import (Nhập): Để sử dụng các biến, hàm hoặc lớp đã được xuất từ một module khác, 
  bạn sử dụng từ khóa import.

Modules cho phép bạn tạo ứng dụng JavaScript lớn hơn, dễ bảo trì hơn và dễ mở rộng hơn bằng cách chia 
thành các phần nhỏ có thể làm việc độc lập. Điều này làm cho việc phát triển và quản lý ứng dụng 
JavaScript trở nên hiệu quả hơn.

Ví dụ:
Giả sử bạn có hai modules: math.js và main.js.

```js
// -----------------------------------------

// --math.js--  Xuất một hàm từ module math.js
export function add(a, b) {
    return a + b;
  }
  
  // Xuất một biến từ module math.js
  export const pi = 3.14159265359;

// ------------------------------------------

// --main.js--  Nhập hàm và biến từ module math.js
import { add, pi } from './math.js';
console.log(add(2, 3)); // Kết quả: 5
console.log(pi); // Kết quả: 3.14159265359
```

Trong ví dụ trên:
Trong module math.js, chúng ta đã xuất hàm add và biến pi bằng cách sử dụng từ khóa export.
Trong module main.js, chúng ta đã nhập hàm add và biến pi bằng cách sử dụng từ khóa import.
Điều này cho phép chúng ta sử dụng chúng trong module main.js

[ Default Exports ]
Ngoài việc xuất nhiều biến và hàm từ một module, bạn có thể xuất một giá trị 
mặc định từ một module. Giá trị mặc định là giá trị mà bạn mong muốn người dùng 
chưa tên khi nhập module. Dưới đây là một ví dụ:

```js
// ------------------------------------------

// --math.js--  Xuất giá trị mặc định từ module math.js
export default function add(a, b) {
    return a + b;
  }
  
// ------------------------------------------

// --main.js--  Nhập giá trị mặc định từ module math.js (không cần dấu ngoặc nhọn)
import add from './math.js';

console.log(add(2, 3)); // Kết quả: 5

// --------------------------------------------------------------------------------------------------------------
```

[ Sử dụng Modules trong Trình Duyệt ]
Để sử dụng modules trong trình duyệt, 
bạn cần thêm thuộc tính type="module" vào thẻ <script> của tệp JavaScript.

```js
<script type="module" src="main.js"></script>
```
