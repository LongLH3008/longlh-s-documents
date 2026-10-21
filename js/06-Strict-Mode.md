# STRICT MODE

    Strict mode là một tính năng trong JavaScript được giới thiệu trong ECMAScript 5 (ES5) để giúp ngăn chặn hoặc cảnh báo về một số lỗi phổ biến và cung cấp một cách nghiêm ngặt hơn để viết mã JavaScript.

    Chế độ "strict mode" là một công cụ mạnh mẽ để giúp bạn viết mã JavaScript an toàn hơn và tránh được nhiều lỗi thông thường. Nó nên được sử dụng trong tất cả các dự án JavaScript để tăng tính tin cậy của mã và giảm thiểu sự cố.

## [ Kích hoạt strict mode ] 
 * Khai báo strict mode:

    Để bật strict mode, bạn cần đặt một chuỗi use strict ở đầu của một hàm hoặc tại đỉnh của một tệp JavaScript. Ví dụ:
```javascript
    "use strict";

    function myFunction() {
        // Mã JavaScript ở đây sẽ chạy trong strict mode
    }
```
 * Hoặc :
```javascript
    "use strict";
    // Mã JavaScript ở đây sẽ chạy trong strict mode
```
* Chú ý rằng khi bạn bật strict mode, nó chỉ áp dụng cho phạm vi của hàm mà nó nằm bên trong hoặc toàn bộ tệp nếu bạn đặt nó ở đầu tệp.

## [ Lợi ích strict mode ] 
    Ném ngoại lệ cho các lỗi thường gặp như việc sử dụng biến chưa được khai báo.

    Ngăn việc sử dụng các từ khóa đặc biệt như eval hay arguments như tên biến.

    Không cho phép gán giá trị cho các biến có thể thay đổi, như NaN hoặc undefined.

    Chế độ nghiêm ngặt hơn khi làm việc với đối tượng, giúp ngăn chặn việc thay đổi bất kỳ đối tượng nào.

## [ Sử dụng strict mode trong các phạm vi khác nhau ]
 * Trong hàm :
```javascript
function myFunction() {
    "use strict";
    // Mã JavaScript ở đây sẽ chạy trong strict mode
}
```
 * Trong phạm vi toàn cục (tại đầu tệp) :
```javascript
"use strict";
// Mã JavaScript ở đây sẽ chạy trong strict mode
```
 * Trong phạm vi của một IIFE (Immediately Invoked Function Expression) :
```javascript
(function () {
    "use strict";
    // Mã JavaScript ở đây sẽ chạy trong strict mode
})();
```
