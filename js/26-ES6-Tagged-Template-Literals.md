# ES6: Tagged Template Literals

## Cơ bản

[ TAGGED TEMPLATE LITERIALS ]
Tagged template literals là một tính năng mạnh mẽ trong ECMAScript 2015 (ES6) cho phép bạn tạo chuỗi 
nội suy (interpolated strings) tùy chỉnh thông qua một hàm gọi đặc biệt. Khi bạn sử dụng tagged template 
literals, bạn có thể xử lý các giá trị được nội suy một cách linh hoạt trước khi chúng được kết hợp vào 
chuỗi cuối cùng. Điều này rất hữu ích trong việc xây dựng chuỗi phức tạp, xử lý văn bản, hoặc tránh tình 
trạng Injection Attack.

```js
// -----------------------------------------------------------------------------------------------------------------------

// [ Cú pháp của tagged template literals là ]

tagFunction`template string`
```

Trong đó tagFunction là một hàm đặc biệt, được gọi là "tag function," và template string là chuỗi cần 
nội suy. Tag function nhận vào các phần của chuỗi và các giá trị được nội suy dưới dạng các tham số 
và có thể thực hiện xử lý tùy chỉnh trước khi trả về kết quả.

```js
// -----------------------------------------------------------------------------------------------------------------------

// [ ví dụ đơn giản về cách sử dụng tagged template literals ]
```

Tag function có thể xử lý các phần của chuỗi và các giá trị được nội suy bằng cách sử dụng chúng 
trong cách phù hợp với mục đích của bạn. Ví dụ, bạn có thể kết hợp chuỗi này lại với các giá trị 
theo cách đặc biệt hoặc thậm chí xử lý chúng trước khi xuất ra kết quả cuối cùng.

```js
function myTag(strings, ...values) {
    console.log(strings); // Mảng chứa các phần của chuỗi không được nội suy
    console.log(values); // Mảng chứa các giá trị được nội suy
  }
  
  const name = 'Alice';
  const age = 30;
  myTag`Hello, my name is ${name} and I am ${age} years old.`;

// => Kết quả của ví dụ trên sẽ là:
[ 'Hello, my name is ', ' and I am ', ' years old.' ]
[ 'Alice', 30 ]

// -----------------------------------------------------------------------------------------------------------------------

// [ ví dụ khác về cách sử dụng tagged template literals để định dạng ngày tháng ]

// Trong ví dụ này, formatDate là một tag function nhận các giá trị được nội suy (năm, tháng, ngày) 
// và biến chúng thành một chuỗi ngày tháng định dạng.

function formatDate(strings, ...values) {
    const date = new Date(...values);
    return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()}`;
  }
  
  const year = 2023;
  const month = 7;
  const day = 12;
  
  const formattedDate = formatDate`Today is ${year}-${month}-${day}.`;
  console.log(formattedDate); // Kết quả: "Today is 2023-7-12."
```

---

## Nâng cao

sử dụng tagged template literals trong JavaScript để tạo một chuỗi HTML được đánh dấu 
(highlighted) từ các giá trị và chuỗi gốc.

nhận một mảng [first, ...strings] là các phần của chuỗi không được nội suy và
một số lượng bất kỳ các giá trị được nội suy ...values.

```js
function hightLight ([first, ...strings], ...values) {
```

+ fisrt
  'Học lập trình'

+ ... strings các phần tử chuỗi k được nội suy:
  'tại'
  '!'

+ ...values các giá trị được nội suy
  'FPT'
  'Js'

Sử dụng .reduce(), nó duyệt qua mảng values. 

Trong mỗi lần lặp, nó thêm một phần tử mới vào mảng acc, 
là một chuỗi HTML <span>${curr}</span> để đánh dấu giá trị hiện tại curr

Sau đó, nó thêm một phần của chuỗi ban đầu sử dụng 
strings.shift().  loại bỏ và trả về phần tử đầu tiên của mảng strings, 
do đó mỗi giá trị trong values sẽ được kết hợp với một phần của chuỗi ban đầu.

lần lặp 1:
  Học lập trình  Js  tại
  ...acc                  : Học lập trình
  `<span>${curr}</span>`  : Js
  strings.shift()         : tại (xóa phần tử đầu tiên và trả về phần tử đã xóa)

lặp lần 2:
  => chuỗi mới ...acc: [ Học lập trình, Js, tại ]
  `<span>${curr}</span>`  : FPT
  strings.shift()         : ! (phần tử đầu có giá trị 'tại' đã xóa nên phần tử tiếp theo là '!')

```js
return values.reduce(
    (acc, curr) => [...acc, `<span>${curr}</span>`, strings.shift()],
    [first]
).join('');
```

.join(): nối các phần tử trong mảng thành 1 chuỗi
Kết quả được in ra sẽ là một chuỗi HTML: "Học lập trình <span>Js</span> tại <span>FPT</span> !", 
trong đó "Js" và "FPT" được đánh dấu bằng thẻ <span>.

```js
}

var brand = 'FPT'
var course = 'Js'

const html = hightLight `Học lập trình ${course} tại ${brand} !`;
console.log(html);
```
