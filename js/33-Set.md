# Set

### **Giới thiệu về `Set` trong JavaScript**

`Set` trong JavaScript là một đối tượng được sử dụng để lưu trữ các giá trị duy nhất, không trùng lặp. Nó có thể chứa bất kỳ kiểu dữ liệu nào (bao gồm cả đối tượng, mảng, v.v.). Điều này giúp `Set` rất hữu ích trong các tình huống mà bạn cần đảm bảo rằng tất cả các phần tử là duy nhất.

---

### **Cú pháp cơ bản của `Set`**

#### **Tạo một `Set`:**

```javascript
let mySet = new Set();
```

Hoặc bạn có thể khởi tạo một `Set` từ một mảng:

```javascript
let mySet = new Set([1, 2, 3, 4]);
```

---

### **Các phương thức của `Set`**

1. **`.add(value)`**

      - Thêm một giá trị vào `Set`. Nếu giá trị đã tồn tại, nó sẽ không được thêm vào.

      ```javascript
      mySet.add(5); // Thêm 5 vào Set
      mySet.add(3); // Không thêm 3 vì 3 đã có trong Set
      ```

2. **`.has(value)`**

      - Kiểm tra xem một giá trị có tồn tại trong `Set` hay không. Trả về `true` nếu có, ngược lại trả về `false`.

      ```javascript
      console.log(mySet.has(3)); // true
      console.log(mySet.has(6)); // false
      ```

3. **`.delete(value)`**

      - Xóa một giá trị khỏi `Set`. Trả về `true` nếu xóa thành công, hoặc `false` nếu không tìm thấy giá trị.

      ```javascript
      mySet.delete(4); // Xóa 4 khỏi Set
      console.log(mySet.has(4)); // false
      ```

4. **`.clear()`**

      - Xóa tất cả các giá trị trong `Set`.

      ```javascript
      mySet.clear();
      console.log(mySet.size); // 0
      ```

5. **`.size`**

      - Trả về số lượng các giá trị trong `Set`.

      ```javascript
      console.log(mySet.size); // 3
      ```

6. **`.forEach(callback)`**

      - Lặp qua tất cả các giá trị trong `Set` và thực thi hàm callback cho mỗi phần tử.

      ```javascript
      mySet.forEach((value) => {
      	console.log(value);
      });
      ```

7. **`.values()`**

      - Trả về một iterator của tất cả các giá trị trong `Set`.

      ```javascript
      const values = mySet.values();
      for (let value of values) {
      	console.log(value); // Lặp qua và in các giá trị
      }
      ```

8. **`.entries()`**

      - Trả về một iterator với mỗi phần tử là một mảng `[value, value]` (vì `Set` không có các cặp khóa-giá trị như trong `Map`).

      ```javascript
      const entries = mySet.entries();
      for (let entry of entries) {
      	console.log(entry); // Lặp qua và in [value, value]
      }
      ```

9. **`.keys()`**

      - Giống với `.values()` trong `Set`, vì `Set` chỉ chứa các giá trị duy nhất, `.keys()` cũng trả về iterator chứa các giá trị.

      ```javascript
      const keys = mySet.keys();
      for (let key of keys) {
      	console.log(key); // Lặp qua và in các giá trị
      }
      ```

---

### **Lợi ích của `Set`**

1. **Giữ các giá trị duy nhất**:  
   `Set` đảm bảo rằng tất cả các phần tử trong nó là duy nhất. Điều này rất hữu ích trong các trường hợp bạn cần loại bỏ các phần tử trùng lặp.

      ```javascript
      let numbers = new Set([1, 2, 2, 3, 4, 4]);
      console.log(numbers); // Set {1, 2, 3, 4}
      ```

2. **Không duy trì thứ tự các phần tử**:  
   Các phần tử trong `Set` luôn được giữ theo thứ tự mà bạn thêm vào, không giống như đối tượng (Object), thứ tự các phần tử trong `Set` là đảm bảo.

3. **Dễ dàng kiểm tra sự tồn tại của một phần tử**:  
   Phương thức `.has()` trong `Set` nhanh chóng xác nhận sự tồn tại của một phần tử mà không cần phải lặp qua tất cả các phần tử như với mảng.

4. **Lặp qua phần tử trong `Set`**:  
   Bạn có thể dễ dàng lặp qua tất cả các phần tử trong `Set` bằng cách sử dụng các phương thức như `.forEach()`, `.values()`, `.entries()`, v.v.

---

### **Các ví dụ sử dụng `Set`**

1. **Loại bỏ phần tử trùng lặp trong mảng:**

      ```javascript
      let arr = [1, 2, 2, 3, 4, 4, 5];
      let uniqueArr = [...new Set(arr)];
      console.log(uniqueArr); // [1, 2, 3, 4, 5]
      ```

2. **Kiểm tra phần tử có tồn tại trong `Set`:**

      ```javascript
      let mySet = new Set([1, 2, 3, 4, 5]);
      console.log(mySet.has(3)); // true
      console.log(mySet.has(6)); // false
      ```

3. **Lặp qua các phần tử trong `Set`:**

      ```javascript
      let mySet = new Set([1, 2, 3, 4, 5]);
      mySet.forEach((value) => {
      	console.log(value); // In các giá trị: 1, 2, 3, 4, 5
      });
      ```

4. **Thêm và xóa phần tử trong `Set`:**

      ```javascript
      let mySet = new Set([1, 2, 3]);
      mySet.add(4); // Thêm 4 vào Set
      mySet.delete(2); // Xóa 2 khỏi Set
      console.log(mySet); // Set {1, 3, 4}
      ```

5. **Sử dụng `Set` để tìm các phần tử chung giữa hai mảng:**

      ```javascript
      let array1 = [1, 2, 3, 4, 5];
      let array2 = [4, 5, 6, 7, 8];

      let set1 = new Set(array1);
      let set2 = new Set(array2);

      let intersection = [...set1].filter((x) => set2.has(x));
      console.log(intersection); // [4, 5]
      ```

6. **Sử dụng `Set` để tìm phần tử khác biệt giữa hai mảng:**

      ```javascript
      let array1 = [1, 2, 3, 4, 5];
      let array2 = [4, 5, 6, 7, 8];

      let set1 = new Set(array1);
      let set2 = new Set(array2);

      let difference = [...set1].filter((x) => !set2.has(x));
      console.log(difference); // [1, 2, 3]
      ```

---

### **Kết luận**

- **`Set`** là một công cụ mạnh mẽ trong JavaScript, đặc biệt khi bạn cần đảm bảo rằng các phần tử trong tập hợp là duy nhất và không bị trùng lặp.
- **Các phương thức** của `Set` giúp dễ dàng thêm, kiểm tra, xóa và lặp qua các phần tử.
- **`Set`** rất hữu ích khi làm việc với dữ liệu lớn, nơi việc đảm bảo tính duy nhất của các phần tử là quan trọng và hiệu suất cần được tối ưu hóa.
