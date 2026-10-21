# Short Circuiting

## Short Circuiting

Short circuiting là một kỹ thuật trong lập trình được sử dụng trong biểu thức logic để cải thiện hiệu suất và tối ưu hóa mã nguồn. Khi một biểu thức logic sử dụng toán tử AND (`&&`) hoặc OR (`||`), short circuiting có thể xảy ra khi kết quả của biểu thức có thể được xác định dựa trên giá trị của một phần của biểu thức mà không cần phải kiểm tra toàn bộ biểu thức.

### Short circuiting với toán tử AND (`&&`):

- **Ngắn mạch khi giá trị đầu tiên là false:**
  ```javascript
  let result = false && someFunction();
  // Kết quả: false
  // someFunction() không được gọi vì giá trị đầu tiên là false
  ```

- **Không ngắn mạch khi giá trị đầu tiên là true:**
  ```javascript
  let result = true && someFunction();
  // Kết quả: Kết quả của someFunction() sẽ ảnh hưởng đến giá trị cuối cùng
  ```

### Short circuiting với toán tử OR (`||`):

- **Ngắn mạch khi giá trị đầu tiên là true:**
  ```javascript
  let result = true || someFunction();
  // Kết quả: true
  // someFunction() không được gọi vì giá trị đầu tiên là true
  ```

- **Không ngắn mạch khi giá trị đầu tiên là false:**
  ```javascript
  let result = false || someFunction();
  // Kết quả: Kết quả của someFunction() sẽ ảnh hưởng đến giá trị cuối cùng
  ```

### Ứng dụng thực tế:

1. **Kiểm tra giá trị trước khi thực hiện một hành động:**
   ```javascript
   let isValidInput = userInput && validateInput(userInput);
   // Nếu userInput là falsy, validateInput() không được gọi, giảm tải hệ thống
   ```

2. **Xử lý giá trị mặc định:**
   ```javascript
   let userRole = getUserRole() || 'Guest';
   // Nếu getUserRole() trả về falsy, userRole sẽ được gán giá trị 'Guest'
   ```

3. **Tránh lỗi khi truy cập thuộc tính của đối tượng không tồn tại:**
   ```javascript
   let usernameLength = user && user.profile && user.profile.username.length;
   // Ngắn mạch nếu user hoặc user.profile là falsy, tránh lỗi truy cập
   ```

Lưu ý rằng khi sử dụng short circuiting, cần hiểu rõ logic của mã nguồn và đảm bảo không tạo ra các hiểu lầm không mong muốn.

---

## Truthy & Falsy

Truthy và Falsy là các khái niệm trong JavaScript mô tả cách giá trị của một biểu thức được đánh giá trong ngữ cảnh boolean. Dưới đây là danh sách đầy đủ các giá trị truthy và falsy trong JavaScript:

### Truthy:

1. Số không phải 0: `1`, `2`, `-1`,...
2. Chuỗi không rỗng: `'hello'`, `'123'`,...
3. Mảng không rỗng: `[]`, `[1, 2, 3]`,...
4. Đối tượng: `{}`, `{ key: 'value' }`,...
5. Toán tử logic `true`: `true`

### Falsy:

1. Số 0: `0`
2. Chuỗi rỗng: `''`
3. `null`
4. `undefined`
5. `NaN` (Not a Number)
6. `false`
7. Document.all (có thể không hỗ trợ trong một số trình duyệt cụ thể)

### Ví dụ:

```javascript
if ('hello') {
  console.log('Truthy!'); // Sẽ được in ra vì chuỗi không rỗng là truthy
}

if (0) {
  console.log('Falsy!'); // Không được in ra vì số 0 là falsy
}

let value = false || 'default';
console.log(value); // 'default', vì false là falsy nên giá trị sau toán tử || được chọn

let username = ''; // username là falsy
let displayName = username || 'Guest';
console.log(displayName); // 'Guest', vì chuỗi rỗng là falsy nên giá trị sau toán tử || được chọn
```

Những khái niệm truthy và falsy là quan trọng trong việc xây dựng các biểu thức điều kiện và đặc biệt hữu ích khi sử dụng toán tử short-circuiting (`&&` và `||`).
