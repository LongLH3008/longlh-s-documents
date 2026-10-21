# Map

### **Giới thiệu về `Map` trong JavaScript**

`Map` là một đối tượng trong JavaScript dùng để lưu trữ các cặp khóa-giá trị (key-value) với các tính năng ưu việt hơn so với đối tượng thông thường (Object). Trong `Map`, bạn có thể sử dụng bất kỳ kiểu dữ liệu nào làm khóa, bao gồm đối tượng, mảng, chuỗi, số, v.v. Điều này làm cho `Map` trở thành một công cụ rất linh hoạt khi làm việc với dữ liệu.

---

### **Cú pháp cơ bản của `Map`**

#### **Tạo mới một `Map`:**

```javascript
let map = new Map();
```

Hoặc bạn có thể tạo một `Map` từ một mảng các cặp **key-value**:

```javascript
let map = new Map([
	["key1", "value1"],
	["key2", "value2"],
]);
```

---

### **Các phương thức của `Map`**

1. **`.set(key, value)`**

      - Thêm hoặc cập nhật một cặp **key-value** vào `Map`.

      ```javascript
      map.set("name", "Alice");
      map.set("age", 30);
      ```

2. **`.get(key)`**

      - Truy xuất giá trị của một khóa (key). Nếu khóa không tồn tại, nó sẽ trả về `undefined`.

      ```javascript
      console.log(map.get("name")); // 'Alice'
      console.log(map.get("age")); // 30
      console.log(map.get("address")); // undefined
      ```

3. **`.has(key)`**

      - Kiểm tra xem khóa có tồn tại trong `Map` hay không. Trả về `true` hoặc `false`.

      ```javascript
      console.log(map.has("name")); // true
      console.log(map.has("address")); // false
      ```

4. **`.delete(key)`**

      - Xóa một cặp **key-value** khỏi `Map`. Trả về `true` nếu xóa thành công, hoặc `false` nếu không tìm thấy khóa.

      ```javascript
      map.delete("age");
      console.log(map.has("age")); // false
      ```

5. **`.clear()`**

      - Xóa toàn bộ cặp **key-value** trong `Map`.

      ```javascript
      map.clear();
      console.log(map.size); // 0
      ```

6. **`.size`**

      - Trả về số lượng cặp **key-value** trong `Map`.

      ```javascript
      console.log(map.size); // 2 (nếu map có 2 cặp key-value)
      ```

7. **`.keys()`**

      - Trả về một iterator của tất cả các khóa trong `Map`.

      ```javascript
      const keys = map.keys();
      for (let key of keys) {
      	console.log(key);
      }
      ```

8. **`.values()`**

      - Trả về một iterator của tất cả các giá trị trong `Map`.

      ```javascript
      const values = map.values();
      for (let value of values) {
      	console.log(value);
      }
      ```

9. **`.entries()`**

      - Trả về một iterator của tất cả các cặp **key-value** dưới dạng mảng `[key, value]`.

      ```javascript
      const entries = map.entries();
      for (let entry of entries) {
      	console.log(entry); // ['key1', 'value1'], ['key2', 'value2']
      }
      ```

10. **`.forEach(callback)`**

      - Lặp qua tất cả các cặp **key-value** trong `Map` và thực thi hàm callback.

      ```javascript
      map.forEach((value, key) => {
      	console.log(`${key}: ${value}`);
      });
      ```

---

### **Lợi ích của `Map` so với `Object`**

1. **Khóa có thể là bất kỳ kiểu dữ liệu nào**:  
   Trong `Map`, bạn có thể sử dụng bất kỳ giá trị nào làm khóa (string, number, object, array, v.v.), trong khi với `Object`, chỉ có chuỗi hoặc symbol có thể làm khóa.

      ```javascript
      const map = new Map();
      map.set({}, "value"); // Thực hiện được trong Map
      ```

2. **Duy trì thứ tự của các khóa**:  
   Các khóa trong `Map` luôn được lưu theo thứ tự mà bạn đã thêm vào (thứ tự chèn vào), trong khi đó `Object` không đảm bảo thứ tự các khóa.

3. **Tính năng tiện lợi**:  
   Các phương thức như `.set()`, `.get()`, `.has()`, và `.delete()` trong `Map` dễ sử dụng hơn rất nhiều so với cách làm việc với đối tượng truyền thống (`Object`).

4. **Có thể lưu trữ được nhiều dữ liệu phức tạp**:  
   Như đã đề cập, bạn có thể sử dụng bất kỳ kiểu dữ liệu nào làm khóa, bao gồm cả đối tượng và mảng.

---

### **Các ví dụ sử dụng `Map`**

1. **Lưu trữ các đối tượng làm khóa:**

      ```javascript
      const map = new Map();
      const objKey = { id: 1 };
      map.set(objKey, "This is a value");
      console.log(map.get(objKey)); // 'This is a value'
      ```

2. **Sử dụng `Map` để đếm tần suất của các từ trong một mảng:**

      ```javascript
      const words = ["apple", "banana", "apple", "orange", "banana", "apple"];
      const wordCount = new Map();

      words.forEach((word) => {
      	wordCount.set(word, (wordCount.get(word) || 0) + 1);
      });

      console.log(wordCount); // Map { 'apple' => 3, 'banana' => 2, 'orange' => 1 }
      ```

3. **Lưu trữ và xử lý dữ liệu phức tạp với `Map`:**

      ```javascript
      const map = new Map();
      map.set("person1", { name: "John", age: 25 });
      map.set("person2", { name: "Alice", age: 30 });

      console.log(map.get("person1").name); // 'John'
      ```

4. **Lưu trữ các cặp khóa-giá trị phức tạp với `Map` (với các đối tượng, mảng):**

      ```javascript
      const map = new Map();
      map.set("user1", { id: 1, hobbies: ["reading", "gaming"] });
      map.set("user2", { id: 2, hobbies: ["coding", "traveling"] });

      console.log(map.get("user1").hobbies); // ['reading', 'gaming']
      ```

---

### **Kết luận**

- **Map** là một công cụ mạnh mẽ để lưu trữ các cặp khóa-giá trị trong JavaScript với nhiều tính năng ưu việt.
- Nó hỗ trợ bất kỳ kiểu dữ liệu nào làm khóa, và bảo toàn thứ tự thêm vào.
- Khi bạn cần xử lý các dữ liệu phức tạp hoặc cần hiệu suất tốt hơn trong việc thêm, truy xuất và xóa các cặp khóa-giá trị, **Map** là một sự lựa chọn tuyệt vời so với **Object**.
