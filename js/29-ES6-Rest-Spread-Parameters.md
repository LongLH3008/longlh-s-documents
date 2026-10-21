# ES6: Rest & Spread (Parameters)

## Rest & Spread

Rest parameters và spread operator là hai tính năng liên quan trong ECMAScript 2015 (ES6) và 
chúng được biểu thị bằng cùng một cú pháp, đó là ba dấu chấm (...). 
Tuy nhiên, chúng được sử dụng trong ngữ cảnh khác nhau và có mục tiêu khác nhau.

=> Rest parameters được sử dụng trong định nghĩa hàm để gom các tham số thành một mảng.

=> Spread operator được sử dụng để trải mảng hoặc đối tượng ra thành các thành phần riêng lẻ hoặc 
để sao chép các giá trị từ một cấu trúc dữ liệu sang một cấu trúc khác.

[ REST ]
Rest parameters được sử dụng trong hàm để chấp nhận một số lượng không xác định các
tham số và gom chúng vào một mảng. Chúng được sử dụng trong định nghĩa hàm để xử lý
các tham số được truyền vào.

```js
// Ví dụ sử dụng rest parameters trong hàm:
```

Trong ví dụ này, ...numbers là rest parameter.
Nó lấy tất cả các tham số được truyền vào và gom chúng vào một mảng numbers.

```js
function sum(...numbers) {
	let total = 0;
	for (const number of numbers) {
		total += number;
	}
	return total;
}

console.log(sum(1, 2, 3, 4, 5)); // Kết quả: 15

// ---------------------------------------------------------------------------------------------------------------
```

[ SPREAD ]
Spread operator, khi sử dụng ngoài ngữ cảnh của hàm, thường được sử dụng để sao chép 
hoặc trải mảng hoặc đối tượng ra thành các thành phần riêng lẻ. Nó có thể được sử dụng 
trong một mảng hoặc đối tượng để tạo một bản sao hoặc trải các giá trị ra để sử dụng.

[ Ví dụ sử dụng spread operator trong mảng ]
Trong ví dụ này, ...numbers là spread operator. Nó trải mảng numbers ra thành các thành 
phần riêng lẻ để được sử dụng trong mảng newNumbers.

```js
const numbers = [1, 2, 3];
const newNumbers = [...numbers, 4, 5];

console.log(newNumbers); // Kết quả: [1, 2, 3, 4, 5]
```

[ Ví dụ sử dụng spread operator trong đối tượng ]
Trong ví dụ này, ...person là spread operator. Nó sao chép tất cả thuộc tính của đối 
tượng person vào đối tượng newPerson.

```js
const person = { name: "John", age: 30 };
const newPerson = { ...person, country: "USA" };

console.log(newPerson);
// Kết quả: { name: 'John', age: 30, country: 'USA' }
```

---

## Bài tập

Thực hành sử dụng Spread

Bạn hãy sử dụng spread để sao chép tất cả các key và value từ object person1 sang person2

```js
const person1 = {
    name: 'Son',
    age: 21
}

const person2 = {
    ...person1
}

// Expected results
console.log(person2.name) // Output: 'Son'
console.log(person2.age) // Output: 21
console.log(person1 === person2) // Output: false
```
