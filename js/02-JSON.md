# JSON

[JSON  - JavaScript Object Notation]

- Là 1 định dạng dữ liệu dạng chuỗi
- Có thể thể hiện dữ liệu dạng : Number,String , Boolean, Null, Array, Object

- Mã hóa ( Encode ) | Giải mã ( Decode )
- .stringify: chuyển đổi Từ Js types -> Json string
- .parse: chuyển đổi Từ json string => js types

```js

var json = 'null'; // chuỗi json thể hiện dữ liệu bool
var json = '["1", "js", "php"]'; // chuỗi json thể hiện dữ liệu array
var json = '{"name": "Long", "age": "18"}'; // chuỗi json thể hiện dữ liệu object 

 var a = '1';
 console.log(JSON.parse(a));
```
