# ES6: Optional Chaining

## Lý thuyết

Optional chaining là một tính năng trong JavaScript giúp tránh gặp lỗi `TypeError` khi truy cập thuộc tính của một đối tượng hoặc lấy giá trị từ một chuỗi các thuộc tính mà có thể không tồn tại. Điều này làm cho mã nguồn trở nên an toàn hơn và giảm khả năng xuất hiện lỗi khi làm việc với dữ liệu không đầy đủ hoặc không xác định.

Cú pháp của optional chaining là `?.`, và nó có thể được sử dụng trong nhiều tình huống khác nhau. Dưới đây là một số ví dụ minh họa:

1. **Truy cập thuộc tính của đối tượng:**
   ```javascript
   let user = {
     address: {
       street: "123 Main St"
     }
   };

   // Truy cập thuộc tính street mà không lo lắng về tồn tại của address
   let street = user?.address?.street;
   console.log(street); // Kết quả: "123 Main St"
   ```

2. **Gọi phương thức của đối tượng:**
   ```javascript
   let car = {
     start: function() {
       console.log("Car started!");
     }
   };

   // Gọi phương thức start mà không quan tâm đến sự tồn tại của car
   car?.start?.(); // In ra: "Car started!"
   ```

3. **Truy cập phần tử trong mảng:**
   ```javascript
   let arr = [1, 2, 3];

   // Truy cập phần tử thứ 1 mà không cần kiểm tra độ dài của mảng
   let element = arr?.[1];
   console.log(element); // Kết quả: 2
   ```

4. **Kiểm tra tồn tại trước khi thực hiện phép toán:**
   ```javascript
   let data = {
     value: 42
   };

   // Thực hiện phép nhân nếu data tồn tại, ngược lại trả về undefined
   let result = data?.value * 2;
   console.log(result); // Kết quả: 84
   ```

Optional chaining là một công cụ mạnh mẽ giúp làm cho mã nguồn JavaScript an toàn hơn và ít lỗi hơn khi xử lý dữ liệu có thể không đầy đủ hoặc không chắc chắn. Tính năng này thường được sử dụng trong các kịch bản hiện đại của JavaScript.

---

## Ví dụ

[ OPTIONAL CHAINING ]

Optional chaining (?.) là một tính năng quan trọng trong JavaScript, 
đặc biệt là trong ECMAScript 2020 (ES11), không phải trong ES6. 

Optional chaining cho phép bạn truy xuất thuộc tính của một đối tượng 
một cách an toàn mà không cần kiểm tra xem đối tượng hoặc thuộc tính 
có tồn tại hay không. 

Điều này giúp tránh lỗi TypeError khi truy xuất 
thuộc tính của một đối tượng không tồn tại.

Optional chaining giúp làm cho mã nguồn trở nên an toàn hơn và dễ đọc 
hơn khi bạn cần truy xuất các thuộc tính hoặc phần tử của đối tượng và 
mảng trong JavaScript.

```js
// -----------------------------------------------------------------------------------------------------------------

// Cú pháp của optional chaining là ?.

// -----------------------------------------------------------------------------------------------------------------
```

[ Truy xuất thuộc tính của đối tượng ]
Trong ví dụ này, person.address?.street cho phép bạn truy xuất thuộc tính street của 
đối tượng person.address, và nếu person.address không tồn tại, nó sẽ trả về undefined 
thay vì gây ra lỗi.

```js
const person = {
    name: 'John',
    address: {
      street: '123 Main St',
      city: 'New York',
    },
  };
  
  const street = person.address?.street;
  console.log(street); // Kết quả: '123 Main St'
  
  const zip = person.address?.zipCode;
  console.log(zip); // Kết quả: undefined (không có lỗi)

// -----------------------------------------------------------------------------------------------------------------
```

[ Truy xuất phần tử của mảng ]
Trong ví dụ này, numbers[5]?.toString() cho phép bạn truy xuất phần tử thứ 5 của mảng 
numbers và sau đó gọi phương thức toString() trên nó. Nếu phần tử thứ 5 không tồn tại, 
nó sẽ trả về undefined thay vì gây ra lỗi.

```js
const numbers = [1, 2, 3, 4, 5];

const first = numbers[0];
console.log(first); // Kết quả: 1

const sixth = numbers[5];
console.log(sixth); // Kết quả: undefined (không có lỗi)

const seventh = numbers[5]?.toString();
console.log(seventh); // Kết quả: undefined (không có lỗi)
```
