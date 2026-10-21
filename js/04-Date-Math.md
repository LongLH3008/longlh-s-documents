# Date & Math

## Date

DATE

```js
// thời gian hiện tại
var date = new Date(); // kiểu object
var date1 = Date(); // kiểu string

var year = date.getFullYear(); // lấy năm
var month = date.getMonth() + 1; // lấy tháng () 0 - 11 )
var day = date.getdate(); // lấy ngày
var hours = date.getHours(); // lấy giờ
var minutes = date.getMinutes(); // lấy phút
var second = date.getSeconds(); // lấy giây

console.log(`${hours}/${minutes}/${second}/${day}/${month}/${year}`);
```

---

## Math

MATH OBJECT

Math.PI
Math.round()
Math.abs()
Math.cell()
Math.floor()
Math.random()
Math.min()
Math.max()

```js
// -----------------------------------------------------------------------------------------
// Math.PI : Trả về số pi 3.14
console.log(Math.PI());
// kết quả => 3.14

// -----------------------------------------------------------------------------------------
// Math.round() : Làm tròn số thập phân
console.log(Math.round(1.3));
// kết quả => 1

// -----------------------------------------------------------------------------------------
// Math.abs() : Giá trị tuyệt đối, biến số âm thành số dương
console.log(Math.abs(-3));
// kết quả => 3

// -----------------------------------------------------------------------------------------
// Math.cell() : làm tròn trên
console.log(Math.cell(1.3));
// kết quả => 2

// -----------------------------------------------------------------------------------------
// Math.floor() : làm tròn dưới
console.log(Math.floor(1.3));
// kết quả => 1

// -----------------------------------------------------------------------------------------
// Math.random() : Trả về số thập phân ngẫu nhiên nhỏ hơn 1, có thể xác định trong 1 khoảng cho trước
// random trong khoảng 0 - 99
console.log(Math.random() * 100);
// kết quả => random

// -----------------------------------------------------------------------------------------
// Math.min() : Lấy giá trị nhỏ nhất trong khoảng
console.log(Math.min(-100,1,99,50,20));
// kết quả => -100

// -----------------------------------------------------------------------------------------
// Math.max() : Lấy giá trị lớn nhất trong khoảng
console.log(Math.max(-100,1,99,50,20));
// kết quả => 99
```
