# Function

## Function

HÀM ( FUNCTION )
- Là 1 khối mã
- Làm 1 việc cụ thể

(*) Loại hàm :
- Built-in : những hàm được cung cấp sẵn
- Declaration : hàm  Tư định nghĩa
- Expression
- Arrow

(*) Tính chất :
- Không thực thi khi định nghĩa
- Sẽ thực thi khi được gọi
- Có thể nhận tham số
- Có thể trả về  giá trị
- Khi định nghĩa là 2 function cùng tên, function định nghĩa sau cùng sẽ
được ghi đè lên function cùng tên trước dó

```js
function showDialog() {
    alert('Hello World !');
}
showDialog();

// Có tham số
function writeLog(messasge) {
    console.log(messasge);
}
writeLog('Hello ')

// Trả về
function plus(a,b){
    return a + b;
}
var result = plus(2,8);
console.log(result);
```

Khái báo biến trong hàm :
- Biến được khai báo trong hàm sẽ chỉ sử dụng được trong phạm vi trong hàm

```js
function showMess() {
    var fulname = 'Son Tung'
}
```

Hàm trong hàm :
- Hàm được khai báo trong 1 hàm sẽ chỉ gọi được và thực thi trong hàm bao chứa nó

```js
function mess1() {
    function mess2() {
        console.log('mess2');
    }
    mess2();
}
```

Declaration Function : Hàm tự định nghĩa
- Có thể gọi hàm trước khi định nghĩa

```js
function mess() {
}
```

Expression Function : Hàm ẩn danh
- Không thể gọi hàm trước khi định nghĩa

```js
var showmess2 = function mess2() {
}

setTimeout(function name() {
})

var Object = {
    myFunction: function () {
    }
}
```

---

## Đệ quy

ĐỆ QUY là 1 function gọi lại chính nó, 1 vòng lặp vô hạn

+ Cần phải xác định điểm dừng trong đệ quy tránh tạo vòng lặp vô hạn
+ Locgic handle => Tạo ra diểm dừng

```js
function giaiThua(number) {
    // Logic handle
    if (number > 0) {
        // gọi lại giaiThua(number - 1) và tiêp tục
        return number * giaiThua (giaiThua - 1);
    }
    // khi k thỏa mãn điều kiện sẽ dừng để quy, trả về 1
    return 1;
}

console.log(giaiThua(3));
```
