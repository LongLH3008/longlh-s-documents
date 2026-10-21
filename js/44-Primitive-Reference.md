# Primitive & Reference Types

## Lý thuyết

Trong JavaScript, có hai loại kiểu dữ liệu chính: Primitive Types (kiểu nguyên thủy) và Reference Types (kiểu tham chiếu).

### Primitive Types (Kiểu nguyên thủy):

1. **Number**: Lưu trữ số, cả số nguyên và số thập phân.
   
   ```javascript
   let number = 10;
   ```

2. **String**: Lưu trữ chuỗi ký tự.
   
   ```javascript
   let string = "Hello!";
   ```

3. **Boolean**: Lưu trữ giá trị true hoặc false.
   
   ```javascript
   let bool = true;
   ```

4. **Undefined**: Biến chưa được gán giá trị.
   
   ```javascript
   let undefinedValue;
   ```

5. **Null**: Biến được gán giá trị null.
   
   ```javascript
   let nullValue = null;
   ```

6. **Symbol**: Được sử dụng cho các giá trị không thay đổi và duy nhất.
   
   ```javascript
   let symbol = Symbol('description');
   ```

### Reference Types (Kiểu tham chiếu):

1. **Object**: Là một tập hợp các cặp key-value, hoặc các thuộc tính và phương thức.
   
   ```javascript
   let object = { key: "value" };
   ```

2. **Array**: Là một loại đặc biệt của object, lưu trữ một tập hợp các giá trị.
   
   ```javascript
   let array = [1, 2, 3];
   ```

3. **Function**: Là một đối tượng có thể được gọi để thực thi một tập hợp các câu lệnh.
   
   ```javascript
   function myFunction() {
       // Some code
   }
   ```

4. **Date**: Được sử dụng để làm việc với ngày tháng.
   
   ```javascript
   let currentDate = new Date();
   ```

### Sự khác biệt giữa Primitive Types và Reference Types:

1. **Lưu trữ trong bộ nhớ**: Primitive Types lưu trữ giá trị trực tiếp trong vùng nhớ, trong khi Reference Types lưu trữ một tham chiếu (địa chỉ) tới vùng nhớ chứa dữ liệu.

2. **Tham chiếu và sao chép**: Khi bạn gán một biến kiểu tham chiếu cho một biến khác, cả hai biến sẽ trỏ tới cùng một vùng nhớ. Trong khi đó, khi bạn gán một biến kiểu nguyên thủy cho một biến khác, một bản sao của giá trị được tạo ra.

```javascript
// Kiểu nguyên thủy
let a = 10;
let b = a; // b là một bản sao của giá trị 10
a += 10;
console.log(b); // kết quả vẫn là 10 vì b là tham trị lưu trữ dữ liệu trực tiếp
```

```js
// Kiểu tham chiếu
let obj1 = { value: 10 };
let obj2 = obj1; // obj2 tham chiếu đến cùng một đối tượng như obj1
obj1.value = 20;
console.log(obj2.value); // Kết quả: 20, do obj2 tham chiếu đến cùng một đối tượng với obj1
```

Điều này quan trọng khi làm việc với các loại dữ liệu này trong JavaScript, đặc biệt khi thay đổi giá trị và muốn sao chép dữ liệu một cách độc lập.

---

## Ví dụ

Các ví dụ giúp hiểu rõ hơn về sự khác biệt giữa kiểu nguyên thủy và kiểu tham chiếu trong JavaScript. Khi làm việc với kiểu tham chiếu, thay đổi một biến có thể ảnh hưởng đến các biến khác tham chiếu đến cùng một vùng nhớ, trong khi kiểu nguyên thủy thì không.

# Kiểu nguyên thủy:

#### Sự sao chép

```javascript
let a = 10;
let b = a; // b là một bản sao của giá trị 10

b = 20;
console.log(a); // Kết quả: 10, vì a và b đều chứa các bản sao riêng biệt của giá trị
```

#### So sánh giá trị

```javascript
let x = 10;
let y = 10;

console.log(x === y); // Kết quả: true, vì cả x và y chứa cùng một giá trị
```
<br>
<hr>

# Kiểu tham chiếu

#### Sự thay đổi trong đối tượng gốc ảnh hưởng đến các tham chiếu

```javascript
let person1 = { name: 'Alice' };
let person2 = person1;

person2.name = 'Bob';

console.log(person1.name); // Kết quả: 'Bob', vì cả person1 và person2 tham chiếu đến cùng một đối tượng
```

#### Gán lại tham chiếu

```javascript
let fruits1 = ['Apple', 'Banana', 'Orange'];
let fruits2 = fruits1;

fruits1 = ['Mango']; // Gán lại giá trị, không thay đổi đối tượng gốc

console.log(fruits1); // Kết quả: ['Mango']
console.log(fruits2); // Kết quả: ['Apple', 'Banana', 'Orange'], vẫn tham chiếu đến đối tượng gốc
```

### Truyền tham chiếu vào hàm

#### Thay đổi đối tượng trong hàm

```javascript
function changeName(person) {
    person.name = 'Charlie';
}

let individual = { name: 'David' };
changeName(individual);

console.log(individual.name); // Kết quả: 'Charlie', vì tham chiếu đến cùng một đối tượng
```

#### Tham số của hàm cũng tham chiếu đến cùng một đối tượng

```javascript
function changeArray(arr) {
    arr.push('Grapes');
}

let fruits = ['Apple', 'Banana'];
changeArray(fruits);

console.log(fruits); // Kết quả: ['Apple', 'Banana', 'Grapes'], vì tham chiếu đến cùng một mảng
```

Những ví dụ này thể hiện cách tham chiếu trong JavaScript cho phép một biến tham chiếu đến một đối tượng và thay đổi trong đối tượng có thể ảnh hưởng đến tất cả các biến tham chiếu tới nó. Điều này quan trọng khi làm việc với đối tượng và mảng trong JavaScript, đặc biệt khi truyền chúng vào hàm hoặc gán chúng cho các biến khác.
