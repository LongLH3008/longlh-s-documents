# Closure

## Closure

# Closure
- Là một hàm có thể ghi nhớ nơi nó thuộc về và truy cập được biến ở bên ngoài phạm vi của nó

- Closure là một tính năng mạnh mẽ trong JavaScript, cho phép chúng ta tạo ra các cấu trúc dữ liệu bảo vệ và duy trì trạng thái của một hàm ngay cả sau khi hàm đó đã kết thúc việc thực thi của mình.

- Một closure xảy ra khi một hàm nằm bên trong một hàm khác và hàm con này có thể sử dụng các biến của hàm cha, ngay cả khi hàm cha đã hoàn thành việc thực thi của mình.

## Ứng dụng
- Viết code ngắn gọn hơn
- Biểu diễn, ứng dụng Private trong OOP

## Lưu ý
- Biến được tham chiếu ( refer ) trong closure sẽ không được xóa khỏi bộ nhớ khi hàm cha chưa thực thi xong

- Các khái niệm Javasscript nâng cao rất dễ gây nhầm lẫn

---

## Ví dụ 1

### #1 Tạo bộ đếm duy trì giá trị

```js
function createCounter() {
    let counter = 0;

    function increase() {
        return ++counter;
    }

    return increase;
}
```

- Hàm createCounter này tạo ra một bộ đếm đơn giản trong JavaScript bằng cách sử dụng closures.

- Biến counter được khởi tạo trong phạm vi của hàm createCounter và chỉ có thể truy cập từ bên trong hàm này. Đây là biến đếm mà chúng ta muốn duy trì giá trị khi chúng ta tạo các hàm tăng giá trị của nó.

- Hàm increase được định nghĩa trong phạm vi của createCounter và trả về kết quả của việc tăng biến counter lên 1 (++counter). Hàm này sử dụng toán tử tăng ++ để tăng giá trị của counter lên trước khi trả về giá trị mới của nó.

- Cuối cùng, hàm increase được trả về từ hàm createCounter.

Khi bạn gọi createCounter(), nó trả về hàm increase. Ví dụ:

```js
    const counter1 = createCounter(); // Tạo một bộ đếm mới
    console.log(counter1()); // Kết quả: 1
    console.log(counter1()); // Kết quả: 2

    const counter2 = createCounter(); // Tạo một bộ đếm khác
    console.log(counter2()); // Kết quả: 1 (không ảnh hưởng đến counter1)
    console.log(counter1()); // Kết quả: 3 (counter1 không bị ảnh hưởng bởi counter2)
```

- Mỗi khi bạn gọi hàm counter1() hoặc counter2(), nó sẽ tăng giá trị của counter mà nó "ghi nhớ" từ phạm vi của hàm createCounter.
- Mỗi lần bạn gọi createCounter(), bạn tạo ra một bộ đếm mới hoàn toàn độc lập với bộ đếm trước đó

---

## Ví dụ 2

### #2 Tạo KeyLogger

```js
function createLogger(namespace) {
    function logger(message) {
        console.log(`[${namespace}] ${message}`);
    }
    return logger;
};
```

- Đoạn code trên định nghĩa một hàm createLogger nhận vào một tham số namespace. Hàm này trả về một hàm khác gọi là logger.

- Hàm logger này nhận một tham số message và sử dụng console.log để in ra thông báo. Thông báo sẽ bao gồm [namespace] được thêm vào trước nội dung của message, nơi namespace là giá trị được truyền vào từ hàm createLogger ban đầu.

- Khi bạn gọi createLogger với một namespace cụ thể, nó sẽ tạo ra một hàm logger mới được cấu hình sẵn để in ra thông báo với namespace đó. Bạn có thể gọi hàm logger trả về và truyền vào một message để in ra thông báo có chứa namespace và message đó.

Ví dụ: 
```js
const infoLogger = createLogger('INFO');
const errorLogger = createLogger('ERROR');

infoLogger('This is an information message'); // In ra: [INFO] This is an information message
errorLogger('This is an error message'); // In ra: [ERROR] This is an error message
```

- Khi bạn gọi createLogger('INFO'), nó tạo ra một hàm logger với namespace là 'INFO'. Hàm logger này sẽ in ra thông báo có chứa [INFO] trước mỗi thông điệp.

- Khi bạn gọi infoLogger('This is an information message'), bạn thực ra đang gọi hàm logger với một tham số message là 'This is an information message'.

- Trong thân của hàm logger, có một dòng code sử dụng console.log để in ra thông điệp. Nó sử dụng namespace (trong trường hợp này là 'INFO') và message (trong trường hợp này là 'This is an information message') để tạo ra thông điệp cuối cùng [INFO] This is an information message và in ra nó trong console.

- Điều quan trọng cần hiểu là hàm logger có thể sử dụng namespace đã được "ghi nhớ" từ lúc createLogger được gọi. Ngay cả khi hàm logger không còn nằm trong phạm vi của createLogger, nó vẫn có thể truy cập vào namespace ban đầu đã được truyền vào để tạo ra hàm logger.

---

## Ví dụ 3

### #3 Làm việc với LocalStorage

```js
function createStorage(key) {
    const store = JSON.parse(localStorage.getItem(key)) ?? {}

    const save = () => {
        localStorage.setItem(key, JSON.stringify(store))
    }

    const storage = {
        get(key) {
            return store[key]
        },
        set(key, value) {
            store[key] = value
            save();
        },
        remove(key) {
            delete store[key]
            save();
        }
    }

    return storage
}
```

- Hàm `createStorage` trong đoạn code trên tạo ra một đối tượng để làm việc với localStorage của trình duyệt. Nó sử dụng closures để bảo vệ dữ liệu và cung cấp các phương thức để thao tác với dữ liệu trong localStorage.
<br>
- `const store = JSON.parse(localStorage.getItem(key)) ?? {}`: Dòng này lấy dữ liệu từ localStorage dựa trên `key` được truyền vào. Nếu không có dữ liệu nào được tìm thấy hoặc dữ liệu không hợp lệ, nó sẽ tạo một đối tượng trống `{}`.
<br>

- `const save = () => { ... }`: Hàm `save` được khai báo để cập nhật dữ liệu trong localStorage. Nó sử dụng `localStorage.setItem` để lưu trữ đối tượng `store` (đã được chuyển đổi thành chuỗi JSON) với `key` tương ứng.
<br>

- `const storage = { ... }`: Đây là đối tượng chứa các phương thức để thao tác với dữ liệu trong `store` và cập nhật dữ liệu trong localStorage thông qua việc gọi hàm `save()`.
<br>
  - `get(key)`: Trả về giá trị từ `store` tương ứng với `key` được truyền vào.
  
  - `set(key, value)`: Đặt giá trị `value` cho `key` tương ứng trong `store`, sau đó gọi `save()` để cập nhật localStorage.
  
  - `remove(key)`: Xóa phần tử có `key` tương ứng từ `store`, sau đó gọi `save()` để cập nhật localStorage.
<br>
- Cuối cùng, hàm trả về một đối tượng `storage` chứa các phương thức `get`, `set`, và `remove`. Khi bạn sử dụng hàm `createStorage` và truyền một `key` cụ thể, bạn sẽ nhận được một đối tượng có thể thao tác với dữ liệu trong localStorage sử dụng các phương thức đã được cung cấp.
<hr>

Ví dụ sử dụng hàm `createStorage`:

```javascript
// Tạo một lưu trữ có key là 'user'
const userStorage = createStorage('user');

// Thêm thông tin người dùng vào lưu trữ
userStorage.set('name', 'John Doe');
userStorage.set('age', 30);

// Lấy thông tin từ lưu trữ
console.log(userStorage.get('name')); // Kết quả: John Doe
console.log(userStorage.get('age')); // Kết quả: 30

// Xóa thông tin từ lưu trữ
userStorage.remove('age');

// Kiểm tra thông tin sau khi xóa
console.log(userStorage.get('age')); // Kết quả: undefined

// Thêm mới thông tin và kiểm tra localStorage
userStorage.set('email', 'john@example.com');

// Kiểm tra localStorage trực tiếp bằng cách lấy giá trị từ 'user'
console.log(JSON.parse(localStorage.getItem('user'))); // Kết quả: { "name": "John Doe", "email": "john@example.com" }
```

- Trong ví dụ này, `createStorage` được sử dụng để tạo một lưu trữ với key là `'user'`. `userStorage` sau đó được sử dụng để thêm, lấy và xóa thông tin từ localStorage theo key `'user'`.

- Khi bạn sử dụng các phương thức như `set`, `get`, và `remove` trên `userStorage`, dữ liệu sẽ được lưu trữ trong localStorage dưới dạng một đối tượng JSON với key `'user'`. Điều này cho phép bạn thêm, lấy và xóa thông tin người dùng trong localStorage một cách dễ dàng và an toàn.
