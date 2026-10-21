# Number

## Number Methods

Dưới đây là một số phương thức quan trọng làm việc với số trong JavaScript, tương tự như phương thức `slice` của mảng:

### 1. **`toFixed(digits)`:**
   - Làm tròn số với số lượng chữ số thập phân được chỉ định.
   ```javascript
   const number = 3.14159;
   const rounded = number.toFixed(2);
   console.log(rounded); // Output: "3.14"
   ```

### 2. **`parseInt(string, radix)`:**
   - Chuyển đổi một chuỗi thành một số nguyên.
   ```javascript
   const str = "123";
   const integer = parseInt(str, 10);
   console.log(integer); // Output: 123
   ```

### 3. **`parseFloat(string)`:**
   - Chuyển đổi một chuỗi thành một số thực.
   ```javascript
   const str = "3.14";
   const floatingPoint = parseFloat(str);
   console.log(floatingPoint); // Output: 3.14
   ```

### 4. **`toPrecision(precision)`:**
   - Định dạng số với số chữ số chính xác được chỉ định.
   ```javascript
   const num = 123.456;
   const formatted = num.toPrecision(4);
   console.log(formatted); // Output: "123.5"
   ```

### 5. **`toString(radix)`:**
   - Chuyển đổi số thành chuỗi theo hệ cơ số chỉ định.
   ```javascript
   const number = 255;
   const binaryString = number.toString(2);
   console.log(binaryString); // Output: "11111111"
   ```

### 6. **`toFixed(fractionDigits)`:**
   - Làm tròn số với số lượng chữ số thập phân được chỉ định.
   ```javascript
   const num = 3.14159;
   const rounded = num.toFixed(2);
   console.log(rounded); // Output: "3.14"
   ```

### 7. **`toExponential(fractionDigits)`:**
   - Chuyển đổi số thành biểu diễn chuỗi có dạng số mũ với số chữ số thập phân được chỉ định.
   ```javascript
   const num = 12345;
   const exponentialNotation = num.toExponential(2);
   console.log(exponentialNotation); // Output: "1.23e+4"
   ```

### 8. **`toPrecision(precision)`:**
   - Định dạng số với số chữ số chính xác được chỉ định.
   ```javascript
   const num = 123.456;
   const formatted = num.toPrecision(4);
   console.log(formatted); // Output: "123.5"
   ```

### 9. **`valueOf()`:**
   - Trả về giá trị nguyên thủy của số.
   ```javascript
   const num = new Number(42);
   const primitiveValue = num.valueOf();
   console.log(primitiveValue); // Output: 42
   ```

### 10. **`isFinite()`:**
   - Kiểm tra xem số có là số hữu hạn hay không.
   ```javascript
   const finiteNumber = 42;
   const isFiniteResult = Number.isFinite(finiteNumber);
   console.log(isFiniteResult); // Output: true
   ```

### 11. **`isNaN()`:**
   - Kiểm tra xem giá trị có phải là NaN hay không.
   ```javascript
   const notANumber = "abc";
   const isNaNResult = Number.isNaN(notANumber);
   console.log(isNaNResult); // Output: true
   ```

### 12. **`isInteger()`:**
   - Kiểm tra xem giá trị có phải là số nguyên hay không.
   ```javascript
   const integerNumber = 42;
   const isIntegerResult = Number.isInteger(integerNumber);
   console.log(isIntegerResult); // Output: true
   ```

### 13. **`isSafeInteger()`:**
   - Kiểm tra xem giá trị có phải là số nguyên an toàn hay không.
   ```javascript
   const safeInteger = Number.MAX_SAFE_INTEGER;
   const isSafeIntegerResult = Number.isSafeInteger(safeInteger);
   console.log(isSafeIntegerResult); // Output: true
   ```

### 14. **`toFixed(fractionDigits)`:**
   - Làm tròn số với số lượng chữ số thập phân được chỉ định.
   ```javascript
   const num = 3.14159;
   const rounded = num.toFixed(2);
   console.log(rounded); // Output: "3.14"
   ```

### 15. **`toPrecision(precision)`:**
   - Định dạng số với số chữ số chính xác được chỉ định.
   ```javascript
   const num = 123.456;
   const formatted = num.toPrecision(4);
   console.log(formatted); // Output: "123.5"
   ```

### 16. **`toString(radix)`:**
   - Chuyển đổi số thành chuỗi theo hệ cơ số chỉ định.
   ```javascript
   const number = 255;
   const binaryString = number.toString(2);
   console.log(binaryString); // Output: "11111111"
   ```

### 17. **`Math.abs(x)`:**
   - Trả về giá trị tuyệt đối của một số.
   ```javascript
   const negativeNumber = -42;
   const absoluteValue = Math.abs(negativeNumber);
   console.log(absoluteValue); // Output: 42
   ```

### 18. **`Math.ceil(x)`:**
   - Làm tròn số lên đến số nguyên gần nhất.
   ```javascript
   const decimalNumber = 3.14;
   const roundedUp = Math.ceil(decimalNumber);
   console.log(roundedUp); // Output: 4
   ```

### 19. **`Math.floor(x)`:**
   - Làm tròn số xuống đến số nguyên gần nhất.
   ```javascript
   const decimalNumber = 3.14;
   const roundedDown = Math.floor(decimalNumber);
   console.log(roundedDown); // Output: 3
   ```

### 20. **`Math.pow(x, y)`:**
   - Trả về giá trị của x mũ y.
   ```javascript
   const base = 2;
   const exponent = 3;
   const result = Math.pow(base, exponent);
   console.log(result); // Output: 8
   ```

### 21. **`Math.sqrt(x)`:**
   - Trả về căn bậc hai của một số.
   ```javascript
   const squareRootValue = Math.sqrt(25);
   console.log(squareRootValue); // Output: 5
   ```

### 22. **`Math.random()`:**
   - Trả về một số ngẫu nhiên từ 0 đến 1 (không bao gồm 1).
   ```javascript
   const randomValue = Math.random();
   console.log(randomValue); // Output: Một số ngẫu nhiên từ 0 đến 1
   ```

### 23. **`Math.round(x)`:**
   - Làm tròn số đến số nguyên gần nhất.
   ```javascript
   const decimalNumber = 3.49;
   const rounded = Math.round(decimalNumber);
   console.log(rounded); // Output: 3
   ```

### 24. **`Math.min(...args)`:**
   - Trả về giá trị nhỏ nhất trong danh sách các số đầu vào.
   ```javascript
   const minNumber = Math.min(5, 3, 8, 2);
   console.log(minNumber); // Output: 2
   ```

### 25. **`Math.max(...args)`:**
   - Trả về giá trị lớn nhất trong danh sách các số đầu vào.
   ```javascript
   const maxNumber = Math.max(5, 3, 8, 2);
   console.log(maxNumber); // Output: 8
   ```

### 26. **`Math.log(x)`:**
   - Trả về logarithm tự nhiên của một số.
   ```javascript
   const logarithmValue = Math.log(10);
   console.log(logarithmValue); // Output: 2.302585092994046
   ```

### 27. **`Math.sin(x)`, `Math.cos(x)`, `Math.tan(x)`:**
   - Trả về giá trị sin, cos, và tan của một góc (đơn vị là radian).
   ```javascript
   const angleInRadians = Math.PI / 4;
   const sinValue = Math.sin(angleInRadians);
   const cosValue = Math.cos(angleInRadians);
   const tanValue = Math.tan(angleInRadians);
   console.log(sinValue, cosValue, tanValue);
   ```

### 28. **`Math.PI`:**
   - Hằng số, giá trị của π (pi).
   ```javascript
   console.log(Math.PI); // Output: 3.141592653589793
   ```

### 29. **`Math.E`:**
   - Hằng số, giá trị của e (số Euler).
   ```javascript
   console.log(Math.E); // Output: 2.718281828459045
   ```

### 30. **`Number.isNaN()`:**
   - Kiểm tra xem giá trị có phải là NaN hay không.
   ```javascript
   const notANumber = "abc";
   const isNaNResult = Number.isNaN(notANumber);
   console.log(isNaNResult); // Output: true
   ```

### 31. **`Number.isInteger()`:**
   - Kiểm tra xem giá trị có phải là số nguyên hay không.
   ```javascript
   const integerNumber = 42;
   const isIntegerResult = Number.isInteger(integerNumber);
   console.log(isIntegerResult); // Output: true
   ```

### 32. **`Math.trunc(x)`:**
   - Loại bỏ phần thập phân của một số, chỉ giữ phần nguyên.
   ```javascript
   const truncatedNumber = Math.trunc(3.14159);
   console.log(truncatedNumber); // Output: 3
   ```

Dưới đây là một số phương thức và thuộc tính khác liên quan đến số trong JavaScript, mà không lặp lại các phương thức đã được liệt kê trước đó:

### 33. **`Math.sign(x)`:**
   - Trả về dấu của một số, có thể là 1 (dương), -1 (âm), hoặc 0 (nếu số là 0).
   ```javascript
   const signOfNumber = Math.sign(-42);
   console.log(signOfNumber); // Output: -1
   ```

### 34. **`Math.cbrt(x)`:**
   - Trả về căn bậc ba của một số.
   ```javascript
   const cubicRoot = Math.cbrt(8);
   console.log(cubicRoot); // Output: 2
   ```

### 35. **`Math.hypot(...args)`:**
   - Trả về căn bậc hai của tổng bình phương các số đầu vào.
   ```javascript
   const hypotenuse = Math.hypot(3, 4);
   console.log(hypotenuse); // Output: 5
   ```

### 36. **`Math.round(x)`:**
   - Làm tròn số đến số nguyên gần nhất.
   ```javascript
   const decimalNumber = 3.49;
   const rounded = Math.round(decimalNumber);
   console.log(rounded); // Output: 3
   ```

### 37. **`Math.random()`:**
   - Trả về một số ngẫu nhiên từ 0 đến 1 (không bao gồm 1).
   ```javascript
   const randomValue = Math.random();
   console.log(randomValue); // Output: Một số ngẫu nhiên từ 0 đến 1
   ```

### 38. **`Number.parseFloat()`:**
   - Chuyển đổi một chuỗi thành số thực.
   ```javascript
   const floatNumber = Number.parseFloat("3.14");
   console.log(floatNumber); // Output: 3.14
   ```

### 39. **`Number.parseInt()`:**
   - Chuyển đổi một chuỗi thành số nguyên.
   ```javascript
   const integerNumber = Number.parseInt("42");
   console.log(integerNumber); // Output: 42
   ```

---

## Ví dụ

Kiểu số - Number
Trong Javascript có 2 loại số:

Các số thông thường trong Javascript được lưu trữ dưới dạng 64 bit IEEE-754 (còn được gọi là các số phẩy động). Trong hầu hết các trường hợp khi làm việc với Javascript là chúng ta sử dụng loại số này.
Số BigInt là loại số sử dụng để biểu thị số nguyên có độ dài tùy ý.
Vì mức độ thông dụng nên ở đây chúng ta chỉ tìm hiểu về loại số thông thường.

```js
// ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

// Cách khai báo
// Cách thông thường khi ta khai báo một số. Ví dụ là: 1000000 (một triệu)

var million = 1000000;
// Cũng là khai báo số 1000000 nhưng có cách viết khác. Bạn có thể thêm chữ e vào sau số 1 và chỉ định số số không phía sau chữ e như sau:

var million = 1e6; // tương tự: 1000000

// hoặc

var billion = 2e9; // tương tự: 2000000000 (hai tỏi à nhầm hai tỉ)
// Đối tượng Number
// Đối tượng Number trong Javascript là đối tượng giúp chúng ta định nghĩa số và làm việc với số. Chúng ta thường sử dụng các phương thức sau của đối tượng Number khi làm việc với số trong Javascript:

// Kiểm tra xem có phải là giá trị k xác định
console.log(isNaN(billion));

// ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

// Phương thức	Vai trò

// Xác định xem giá trị đã cho có phải là số hữu hạn hay không. Trả về boolean
Number.isFinite()	

// Xác định xem giá trị đã cho có phải là số nguyên hay không. Trả về boolean
Number.isInteger()	

// Chuyển đổi chuỗi đã cho thành một số dấu phẩy động
Number.parseFloat()	

// Chuyển đổi chuỗi đã cho thành một số nguyên
Number.parseInt()	

// Chuyển đổi và trả về chuỗi đại diện cho số đã cho, có số chữ số chính xác sau dấu thập phân
Number.prototype.toFixed()

// Chuyển đổi và trả về số đã cho dưới dạng chuỗi
Number.prototype.toString()	

// Ví dụ:

Number.isFinite(2 / 0); // false
Number.isFinite(20 / 5); // true
Number.isFinite(0 / 0); // false

Number.isInteger(999999999); // true
Number.isInteger(0.2);       // false
Number.isInteger(Math.PI);   // false

Number.parseFloat('10') // 10
Number.parseFloat('10.00') // 10
Number.parseFloat('238,21') // 238
Number.parseFloat('237.22') // 237.22
Number.parseFloat('34 56 78') // 34
Number.parseFloat(' 37 ') // 37
Number.parseFloat('18 is my age') // 18

Number.parseInt('10') // 10
Number.parseInt('10.00') // 10
Number.parseInt('238,21') // 238
Number.parseInt('237.22') // 237
Number.parseInt('34 56 78') // 34
Number.parseInt(' 37 ') // 37
Number.parseInt('18 is my age') // 18

var numberObject = 1234.56789;

numberObject.toFixed(); // '1235'
numberObject.toFixed(1); // '1234.6'
numberObject.toFixed(6); // '1234.567890'

(11).toString();    // '11'
(18).toString();     // '18'
(17.3).toString();   // '17.3'
```

---

## Bài tập

Bài tập: 
Tạo hàm isNumber
Hàm isNumber có 1 tham số đặt tên là value
isNumber(value) trả về true khi value có kiểu số, ngược lại trả về false
isNumber(NaN) cần trả về false

```js
function isNumber (value) {
    return typeof(value) === 'number' && !isNaN(value) ? true : false;
}

// Expected results:
console.log(isNumber(999)); // true
console.log(isNumber('abc')); // false
console.log(isNumber('100')); // false

console.log(isNumber(NaN)); // false
console.log(isNumber(100 / 'abc')); // false
```
