# Array

Dưới đây là một số phương thức mảng (array methods) quan trọng khác trong JavaScript, mỗi phương thức đi kèm với một ví dụ:

### 1. **`push(element1, ..., elementN)`:**

- Thêm một hoặc nhiều phần tử vào cuối mảng.

```javascript
const fruits = ["apple", "banana"];
fruits.push("orange");
console.log(fruits); // Output: ['apple', 'banana', 'orange']
```

### 2. **`pop()`:**

- Loại bỏ phần tử cuối cùng của mảng và trả về phần tử đó.

```javascript
const fruits = ["apple", "banana", "orange"];
const removed = fruits.pop();
console.log(removed); // Output: 'orange'
console.log(fruits); // Output: ['apple', 'banana']
```

### 3. **`shift()`:**

- Loại bỏ phần tử đầu tiên của mảng và trả về phần tử đó.

```javascript
const fruits = ["apple", "banana", "orange"];
const removed = fruits.shift();
console.log(removed); // Output: 'apple'
console.log(fruits); // Output: ['banana', 'orange']
```

### 4. **`unshift(element1, ..., elementN)`:**

- Thêm một hoặc nhiều phần tử vào đầu mảng.

```javascript
const fruits = ["banana", "orange"];
fruits.unshift("apple", "kiwi");
console.log(fruits); // Output: ['apple', 'kiwi', 'banana', 'orange']
```

### 5. **`concat(array1, array2, ..., arrayN)`:**

- Kết hợp hai hoặc nhiều mảng thành một mảng mới.

```javascript
const fruits = ["apple", "banana"];
const moreFruits = ["orange", "kiwi"];
const combined = fruits.concat(moreFruits);
console.log(combined); // Output: ['apple', 'banana', 'orange', 'kiwi']
```

### 6. **`indexOf(element[, fromIndex])`:**

- Trả về index của phần tử đầu tiên trong mảng. Nếu không tìm thấy, trả về -1.

```javascript
const fruits = ["apple", "banana", "orange"];
const index = fruits.indexOf("banana");
console.log(index); // Output: 1
```

### 7. **`reverse()`:**

- Đảo ngược thứ tự các phần tử trong mảng.

```javascript
const fruits = ["apple", "banana", "orange"];
fruits.reverse();
console.log(fruits); // Output: ['orange', 'banana', 'apple']
```

### 8. **`join(separator)`:**

- Chuyển đổi mảng thành chuỗi bằng cách nối các phần tử với nhau, được ngăn cách bởi `separator`.

```javascript
const fruits = ["apple", "banana", "orange"];
const fruitString = fruits.join(", ");
console.log(fruitString); // Output: 'apple, banana, orange'
```

### 9. **`forEach(callback(currentValue, index, array))`:**

- Duyệt qua từng phần tử trong mảng và gọi hàm callback cho mỗi phần tử.

```javascript
const numbers = [1, 2, 3, 4, 5];
numbers.forEach((num, index) => {
	console.log(`Element at index ${index}: ${num}`);
});
// Output:
// Element at index 0: 1
// Element at index 1: 2
// Element at index 2: 3
// Element at index 3: 4
// Element at index 4: 5
```

### 10. **`map(callback(currentValue, index, array))`:**

- Tạo một mảng mới bằng cách gọi hàm callback cho mỗi phần tử và trả về giá trị mới.

```javascript
const numbers = [1, 2, 3, 4, 5];
const squaredNumbers = numbers.map((num) => num * num);
console.log(squaredNumbers); // Output: [1, 4, 9, 16, 25]
```

### 11. **`filter(callback(element, index, array))`:**

- Tạo một mảng mới chứa các phần tử thoả mãn điều kiện của hàm callback.

```javascript
const numbers = [1, 2, 3, 4, 5];
const evenNumbers = numbers.filter((num) => num % 2 === 0);
console.log(evenNumbers); // Output: [2, 4]
```

### 12. **`reduce(callback(accumulator, currentValue, index, array), initialValue)`:**

- Giảm mảng thành một giá trị duy nhất bằng cách thực hiện hàm callback cho từng phần tử.

```javascript
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((acc, num) => acc + num, 0);
console.log(sum); // Output: 15
```

### 13. **`every(callback(element, index, array))`:**

- Kiểm tra xem tất cả các phần tử có thoả mãn điều kiện của hàm callback hay không.

```javascript
const numbers = [2, 4, 6, 8, 10];
const allEven = numbers.every((num) => num % 2 === 0);
console.log(allEven); // Output: true
```

### 14. **`some(callback(element, index, array))`:**

- Kiểm tra xem có ít nhất một phần tử thoả mãn điều kiện của hàm callback hay không.

```javascript
const numbers = [1, 3, 5, 7, 8];
const hasEven = numbers.some((num) => num % 2 === 0);
console.log(hasEven); // Output: true
```

### 15. **`find(callback(element, index, array))`:**

- Trả về giá trị của phần tử đầu tiên trong mảng thoả mãn điều kiện của hàm callback.

```javascript
const numbers = [5, 12, 8, 3, 9];
const firstEven = numbers.find((num) => num % 2 === 0);
console.log(firstEven); // Output: 12
```

### 16. **`findIndex(callback(element, index, array))`:**

- Trả về index của phần tử đầu tiên trong mảng thoả mãn điều kiện của hàm callback.

```javascript
const numbers = [5, 12, 8, 3, 9];
const indexOfFirstEven = numbers.findIndex((num) => num % 2 === 0);
console.log(indexOfFirstEven); // Output: 1
```

Ok, mình sửa lại cho **đúng – đủ – không hở case nào** của `slice`, nhưng vẫn gọn và dễ nhớ 👌

---

### 17. **`slice(start?, end?)`**

- Trả về **một mảng mới** được cắt từ mảng gốc
- **Không làm thay đổi mảng ban đầu (immutable)**
- Cắt từ `start` **đến trước** `end`

- `start`:
     - Không truyền → mặc định là `0`
     - Số âm → tính từ **cuối mảng**

- `end`:
     - Không truyền → cắt tới **hết mảng**
     - Số âm → tính từ **cuối mảng**

- Nếu `start >= end` → trả về mảng rỗng `[]`

```js
const numbers = [1, 2, 3, 4, 5];

// Cắt từ index 1 đến trước index 4
numbers.slice(1, 4); // [2, 3, 4]

// Không truyền end → cắt tới hết
numbers.slice(2); // [3, 4, 5]

// Không truyền start & end → clone mảng
numbers.slice(); // [1, 2, 3, 4, 5]

// Start âm → tính từ cuối
numbers.slice(-2); // [4, 5]

// End âm
numbers.slice(1, -1); // [2, 3, 4]

// Start >= end
numbers.slice(3, 2); // []

// Vượt range → vẫn an toàn
numbers.slice(10); // []
numbers.slice(-10, 2); // [1, 2]
```

### 18. **`splice(start, deleteCount, ...items)`:**

- Thay đổi nội dung của mảng bằng cách xóa hoặc thêm các phần tử tại vị trí được chỉ định.

```JavaScript
const fruits = ['apple', 'banana', 'orange', 'grape'];

// Xóa 1 phần tử từ vị trí index 1
fruits.splice(1, 1);
console.log(fruits); // Output: ['apple', 'orange', 'grape']

// Thêm các phần tử mới tại vị trí index 2
fruits.splice(2, 0, 'kiwi', 'melon');
console.log(fruits); // Output: ['apple', 'orange', 'kiwi', 'melon', 'grape']

// Thay thế 2 phần tử bắt đầu từ index 1 bằng 'pear', 'peach'
fruits.splice(1, 2, 'pear', 'peach');
console.log(fruits); // Output: ['apple', 'pear', 'peach', 'melon', 'grape']
```

### 19. **`includes(element[, fromIndex])`:**

- Kiểm tra xem mảng có chứa phần tử cụ thể hay không.

```javascript
const numbers = [1, 2, 3, 4, 5];
const hasThree = numbers.includes(3);
console.log(hasThree); // Output: true
```

### 20. **`isArray(obj)`:**

- Kiểm tra xem một đối tượng có phải là mảng hay không.

```javascript
const array = [1, 2, 3];
const isArr = Array.isArray(array);
console.log(isArr); // Output: true
```

### 21. **`flat([depth])`:**

- Hợp nhất tất cả các mảng con trong mảng, với sự chấp nhận của độ sâu tối đa (depth).

```javascript
const nestedArray = [1, [2, [3, [4]]]];
const flatArray = nestedArray.flat(2);
console.log(flatArray); // Output: [1, 2, 3, 4]
```

### 22. **`flatMap(callback(currentValue, index, array))`:**

- Tương tự như `map`, nhưng sau đó hợp nhất kết quả thành một mảng duy nhất.

```javascript
const numbers = [1, 2, 3];
const doubledAndFlattened = numbers.flatMap((num) => [num * 2]);
console.log(doubledAndFlattened); // Output: [2, 4, 6]
```

### 23. **`sort([compareFunction])`:**

Phương thức `sort` là một phương thức của đối tượng mảng trong JavaScript được sử dụng để sắp xếp các phần tử trong mảng. Phương thức này thực hiện việc sắp xếp theo mặc định là theo thứ tự Unicode. Tuy nhiên, bạn cũng có thể cung cấp một hàm so sánh tùy chỉnh để xác định cách sắp xếp.

Dưới đây là giải thích chi tiết về cách sử dụng phương thức `sort` và hàm so sánh tùy chỉnh:

- Sử dụng Phương thức `sort` mặc định:

```javascript
const fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.sort();

console.log(fruits);
// Output: ['Apple', 'Banana', 'Mango', 'Orange']
```

Trong trường hợp này, mảng `fruits` sẽ được sắp xếp theo thứ tự Unicode mặc định.

- Sắp xếp theo giá trị số:

```javascript
const numbers = [10, 5, 8, 2, 7];
numbers.sort();

console.log(numbers);
// Output: [10, 2, 5, 7, 8]
```

Ở đây, các số được sắp xếp theo thứ tự Unicode, không phải theo giá trị số.

- Sử dụng Hàm so sánh tùy chỉnh:

```javascript
const numbers = [10, 5, 8, 2, 7];
numbers.sort((a, b) => a - b);

console.log(numbers);
// Output: [2, 5, 7, 8, 10]
```

Hàm so sánh `(a, b) => a - b` được sử dụng để sắp xếp mảng `numbers` theo giá trị số tăng dần.

- Sắp xếp đối tượng theo thuộc tính:

```javascript
const foods = [
	{ name: "Pizza", curFood: 5 },
	{ name: "Burger", curFood: 3 },
	{ name: "Salad", curFood: 8 },
	{ name: "Pasta", curFood: 2 },
];

foods.sort((a, b) => a.curFood - b.curFood);

console.log(foods);
/*
Output:
[
  { name: 'Pasta', curFood: 2 },
  { name: 'Burger', curFood: 3 },
  { name: 'Pizza', curFood: 5 },
  { name: 'Salad', curFood: 8 }
]
*/
```

Trong ví dụ này, mảng `foods` được sắp xếp theo thuộc tính `curFood` của các đối tượng.

Sắp xếp giảm dần:

```javascript
const numbers = [10, 5, 8, 2, 7];
numbers.sort((a, b) => b - a);

console.log(numbers);
// Output: [10, 8, 7, 5, 2]
```

Hàm so sánh `(a, b) => b - a` được sử dụng để sắp xếp mảng `numbers` theo giá trị số giảm dần.

Lưu ý: Hàm so sánh trong phương thức `sort` trả về một số dương nếu `a` nên đứng trước `b`, số âm nếu `a` nên đứng sau `b`, và 0 nếu `a` và `b` bằng nhau.

### 24. **`reverse()`:**

- Đảo ngược thứ tự các phần tử trong mảng.

```javascript
const numbers = [1, 2, 3, 4, 5];
numbers.reverse();
console.log(numbers); // Output: [5, 4, 3, 2, 1]
```

### 25. **`fill(value[, start[, end]])`:**

- Đổ đầy mảng với một giá trị cụ thể từ index `start` đến `end`.

```javascript
const array = [1, 2, 3, 4];
array.fill(0, 1, 3);
console.log(array); // Output: [1, 0, 0, 4]
```

### 26. **`keys()`, `values()`, `entries()`**

- `keys()`: Trả về một iterator chứa các index của mảng.
- `values()`: Trả về một iterator chứa các giá trị của mảng.
- `entries()`: Trả về một iterator chứa cặp [index, value] của mảng.

```javascript
const fruits = ["apple", "banana", "orange"];
const keys = fruits.keys();
console.log([...keys]); // Output: [0, 1, 2]

const values = fruits.values();
console.log([...values]); // Output: ['apple', 'banana', 'orange']

const entries = fruits.entries();
console.log([...entries]); // Output: [[0, 'apple'], [1, 'banana'], [2, 'orange']]
```

### 27. **`reduceRight(callback(accumulator, currentValue, index, array), initialValue)`:**

- Giống như `reduce`, nhưng bắt đầu từ phải sang trái.

```javascript
const numbers = [1, 2, 3, 4, 5];
const sumFromRight = numbers.reduceRight((acc, num) => acc + num, 0);
console.log(sumFromRight); // Output: 15
```

### 28. **`copyWithin(target, start[, end])`:**

- Sao chép một phần của mảng và ghi đè lên chính nó, bắt đầu từ `start` đến `end`, và đặt nó tại vị trí `target`.

```javascript
const array = [1, 2, 3, 4, 5];
array.copyWithin(0, 3, 5);
console.log(array); // Output: [4, 5, 3, 4, 5]
```
