# Polyfill

PolyFil : là một đoạn mã (thường là JavaScript trên Web) được sử dụng để 
cung cấp chức năng hiện đại trên các trình duyệt cũ hơn vốn không hỗ trợ nó.

- includes() :  dùng để kiểm tra xem một chuỗi có chứa một ký tự hoặc
một chuỗi con nào đó hay không. Nếu chuỗi có chứa ký tự hoặc chuỗi
con đó, phương thức sẽ trả về true, ngược lại sẽ trả về false.

```js
if (!String.prototype.includes){
    String.prototype.includes = function (search, start) {
        'use strict'

        if (search instanceof RegExp){
            throw TypeError('first argument must not be a RegExp')
        }

        if (start === undefined) { start = 0; }
        return this.indexOf(search, start) !== -1;
    }
}
```
