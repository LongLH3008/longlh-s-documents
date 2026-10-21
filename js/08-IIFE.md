# IIFE

## IIFE

# IIFE - Immediately Invoked Function Expression

-  Là một biểu thức hàm JavaScript mà bạn  khai báo và gọi ngay lập tức. 
-   IIFE thường được sử dụng để tạo một phạm vi thực thi riêng tư 
    để tránh xung đột biến toàn cục và bảo vệ mã JavaScript khỏi các tác động bên ngoài.
-   Một trong những lợi ích lớn của IIFE là nó tạo ra một phạm vi thực thi riêng tư, giúp bảo 
    vệ biến và hàm khỏi việc ghi đè và xung đột với các biến và hàm ở ngoài phạm vi đó. 
- Điều này rất hữu ích trong việc quản lý mã JavaScript lớn và phức tạp.
- IIFE là hàm private
- Khi sử dụng dùng dấu ; trước IIFE

## Cú pháp cơ bản
-    Mã JavaScript bên trong hàm được đặt trong cặp dấu ngoặc đơn (function(){}), 
    sau đó, ngay sau dấu đóng ngoặc cuối () bạn gọi nó bằng cách thêm () sau dấu đóng ngoặc.

```js
    ;(function() {
        // Mã JavaScript ở đây
    })();
```

- IIFE có thể truyền tham số vào như một hàm bình thường. 
    Dưới đây là ví dụ về việc truyền tham số vào một IIFE:

```js
    ;(function(x, y) {
        var result = x + y;
        console.log(result);
    })(10, 5); // Gọi IIFE và truyền giá trị 10 và 5 vào x và y
```

## Tính đặc trưng (Private)

```js
    const app = (function() {
        // Private
        const cars = []

        return {
            get(index) {
                return cars[index]
            },
            add(car) {
                cars.push(car)
            },
            edit(index, car) {
                cars[index] = car
            },
            delete(index) {
                cars.splice(index, 1)
            }
        }
    })
```

- Đoạn code trên là một ví dụ về cách sử dụng module pattern trong JavaScript để tạo một "module" có các phương thức để quản lý một danh sách các ô tô.

- Đầu tiên, `const app = (function() { ... })` là một hàm tự thực thi ngay lập tức (Immediately Invoked Function Expression - IIFE). Điều này giúp tạo ra một phạm vi (scope) riêng tư, trong đó các biến có thể được bảo vệ và không bị xâm phạm từ bên ngoài.

- Trong phạm vi của hàm này, có một mảng `cars` được khai báo làm biến riêng tư (private). Mảng này sẽ chứa danh sách các đối tượng ô tô.

- Sau đó, hàm trả về một đối tượng có chứa các phương thức để thao tác với mảng `cars`, bao gồm:
  - `get(index)`: Trả về đối tượng ô tô ở vị trí `index` trong mảng `cars`.
  - `add(car)`: Thêm một đối tượng ô tô mới vào cuối mảng `cars`.
  - `edit(index, car)`: Chỉnh sửa thông tin của ô tô ở vị trí `index` trong mảng `cars` thành thông tin của ô tô mới `car`.
  - `delete(index)`: Xóa đối tượng ô tô ở vị trí `index` khỏi mảng `cars`.

Điểm mạnh của module pattern này là giữ cho mảng `cars` được bảo vệ và chỉ có thể được truy cập thông qua các phương thức được cung cấp. Bất kỳ ai sử dụng biến `app` sẽ chỉ có thể thao tác với danh sách ô tô thông qua các phương thức `get`, `add`, `edit`, và `delete`, mà không thể truy cập trực tiếp vào mảng `cars`.

---

## Bài tập

## #1

```js
    const number = (function(a, b) {
        a + b;
    })(1, 2);

    console.log(number) // Output: ?
```

- Answer: 
> Function được khởi tạo không có từ khóa return nên nó sẽ trả về giá trị undefined khi được thực thi.


## #2

```js
    (function js(x) {
        const y = (j) => j * x;

        console.log(y(s()));

        function s() {
            return j();
        }

        function j() {
            return x ** x;
        }
    })(3)
    // Output: ?
```

- Answer: 
> Hàm js() được thực thi ngay lập tức sau khi được khai báo và được gọi là IIFE. Lưu ý rằng tham số x của hàm js được truyền thực tế với giá trị 3.

> Giá trị trả về của hàm là y(s())), nghĩa là gọi ba hàm khác là y(), s() và j() vì hàm s() trả về j().

> j() trả về 3 ^ 3 = 27 để s() trả về27.

> y(s()) có nghĩa là y(27) trả về 27 * 3 = 81.
