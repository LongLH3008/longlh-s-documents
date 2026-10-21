# Keys, Values, Entries

## Giới thiệu

Trong JavaScript, `keys`, `values`, và `entries` là các phương thức có sẵn cho cả mảng (Array) và đối tượng (Object). 

Các phương thức `keys()`, `values()`, và `entries()` cho mảng và các phương thức `Object.keys()`, `Object.values()`, và `Object.entries()` cho đối tượng đều giúp làm việc với dữ liệu cấu trúc dễ dàng và linh hoạt hơn, đặc biệt khi bạn cần duyệt qua hoặc lấy thông tin từ các thành phần cụ thể.

---

## Array

### 1. **Mảng (Array):**

#### a. **`keys()` Method:**
   - Trả về một đối tượng Iterator chứa các chỉ mục (index) của mảng.

   ```javascript
   let arr = ['apple', 'banana', 'orange'];

   // Sử dụng keys()
   let iterator = arr.keys();

   // Duyệt qua các chỉ mục của mảng
   for (let key of iterator) {
     console.log(key); // 0, 1, 2
   }
   ```

#### b. **`values()` Method:**
   - Trả về một đối tượng Iterator chứa các giá trị của mảng.

   ```javascript
   let arr = ['apple', 'banana', 'orange'];

   // Sử dụng values()
   let iterator = arr.values();

   // Duyệt qua các giá trị của mảng
   for (let value of iterator) {
     console.log(value); // 'apple', 'banana', 'orange'
   }
   ```

#### c. **`entries()` Method:**
   - Trả về một đối tượng Iterator chứa các cặp `[key, value]` của mảng.

   ```javascript
   let arr = ['apple', 'banana', 'orange'];

   // Sử dụng entries()
   let iterator = arr.entries();

   // Duyệt qua các cặp [key, value] của mảng
   for (let entry of iterator) {
     console.log(entry); // [0, 'apple'], [1, 'banana'], [2, 'orange']
   }
   ```

---

## Object

### 2. **Đối tượng (Object):**

#### a. **`Object.keys()` Method:**
   - Trả về một mảng chứa các key của đối tượng.

   ```javascript
   let obj = { name: 'John', age: 25, city: 'New York' };

   // Sử dụng Object.keys()
   let keys = Object.keys(obj);

   console.log(keys); // ['name', 'age', 'city']
   ```

#### b. **`Object.values()` Method:**
   - Trả về một mảng chứa các giá trị của đối tượng.

   ```javascript
   let obj = { name: 'John', age: 25, city: 'New York' };

   // Sử dụng Object.values()
   let values = Object.values(obj);

   console.log(values); // ['John', 25, 'New York']
   ```

#### c. **`Object.entries()` Method:**
   - Trả về một mảng chứa các cặp `[key, value]` của đối tượng.

   ```javascript
   let obj = { name: 'John', age: 25, city: 'New York' };

   // Sử dụng Object.entries()
   let entries = Object.entries(obj);

   console.log(entries); // [['name', 'John'], ['age', 25], ['city', 'New York']]
   ```
