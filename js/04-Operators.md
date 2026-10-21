# Toán tử (Operators)

## Operators

Dưới đây là danh sách đầy đủ về các loại toán tử trong JavaScript cùng với ví dụ minh họa cho mỗi loại:

### 1. Toán Tử Số Học:

- **Cộng (`+`):**
  ```javascript
  let result = 5 + 3; // 8
  ```

- **Trừ (`-`):**
  ```javascript
  let result = 5 - 3; // 2
  ```

- **Nhân (`*`):**
  ```javascript
  let result = 5 * 3; // 15
  ```

- **Chia (`/`):**
  ```javascript
  let result = 6 / 3; // 2
  ```

- **Lấy Phần Dư (`%`):**
  ```javascript
  let result = 7 % 3; // 1
  ```

- **Tăng Giá Trị Một Đơn Vị (`++`):**
  ```javascript
  let num = 5;
  num++; // giờ num là 6
  ```

- **Giảm Giá Trị Một Đơn Vị (`--`):**
  ```javascript
  let num = 5;
  num--; // giờ num là 4
  ```

### 2. Toán Tử So Sánh:

- **So Sánh Bằng (`==`):**
  ```javascript
  console.log(5 == '5'); // true
  ```

- **So Sánh Bằng và Cùng Kiểu (`===`):**
  ```javascript
  console.log(5 === '5'); // false
  ```

- **So Sánh Khác (`!=`):**
  ```javascript
  console.log(5 != '5'); // false
  ```

- **So Sánh Khác và Cùng Kiểu (`!==`):**
  ```javascript
  console.log(5 !== '5'); // true
  ```

- **Lớn Hơn (`>`):**
  ```javascript
  console.log(5 > 3); // true
  ```

- **Nhỏ Hơn (`<`):**
  ```javascript
  console.log(5 < 3); // false
  ```

- **Lớn Hơn hoặc Bằng (`>=`):**
  ```javascript
  console.log(5 >= 5); // true
  ```

- **Nhỏ Hơn hoặc Bằng (`<=`):**
  ```javascript
  console.log(5 <= 3); // false
  ```

### 3. Toán Tử Logic:

- **AND (`&&`):**
  ```javascript
  let result = true && false; // false
  ```

- **OR (`||`):**
  ```javascript
  let result = true || false; // true
  ```

- **NOT (`!`):**
  ```javascript
  let result = !true; // false
  ```

### 4. Toán Tử Gán:

- **Gán (`=`):**
  ```javascript
  let x = 5;
  ```

- **Gán và Cộng (`+=`):**
  ```javascript
  let x = 5;
  x += 3; // giờ x là 8
  ```

- **Gán và Trừ (`-=`):**
  ```javascript
  let x = 5;
  x -= 3; // giờ x là 2
  ```

- **Gán và Nhân (`*=`):**
  ```javascript
  let x = 5;
  x *= 3; // giờ x là 15
  ```

- **Gán và Chia (`/=`):**
  ```javascript
  let x = 6;
  x /= 3; // giờ x là 2
  ```

### 5. Toán Tử Bitwise:

- **AND Bitwise (`&`):**
  ```javascript
  let result = 5 & 3; // 1
  ```

- **OR Bitwise (`|`):**
  ```javascript
  let result = 5 | 3; // 7
  ```

- **XOR Bitwise (`^`):**
  ```javascript
  let result = 5 ^ 3; // 6
  ```

- **NOT Bitwise (`~`):**
  ```javascript
  let result = ~5; // -6
  ```

- **Dịch Trái (`<<`):**
  ```javascript
  let result = 5 << 1; // 10
  ```

- **Dịch Phải (`>>`):**
  ```javascript
  let result = 5 >> 1; // 2
  ```

### 6. Toán Tử Điều Kiện (Ternary):

- **Ternary (`condition ? expr1 : expr2`):**
  ```javascript
  let result = (5 > 3) ? 'true' : 'false'; // 'true'
  ```

### 7. Toán Tử Nullish Coalescing (`??`):

- **Nullish Coalescing (`a ?? b`):**
  ```javascript
  let firstName = null;
  let lastName = 'Doe';

  let fullName = firstName ?? lastName; // 'Doe'
  ```

---

## Ternary Operator

### Toán Tử Ba Ngôi (Ternary Operator):

- **Ternary (`condition ? expr1 : expr2`):**
  - Kết quả là `expr1` nếu `condition` đúng, ngược lại là `expr2`.

  ```javascript
  let result = (5 > 3) ? 'true' : 'false'; // 'true'
  ```

Toán tử ba ngôi là cách rất ngắn gọn để thực hiện một kiểm tra và trả về một giá trị tùy thuộc vào kết quả của kiểm tra đó.
