# Circular Dependencies

## 1. Định nghĩa kỹ thuật

**Circular dependency** (phụ thuộc vòng) xảy ra khi trong **đồ thị phụ thuộc** giữa các module/class/service tồn tại một **chu trình**: A phụ thuộc B và B (trực tiếp hoặc gián tiếp qua C, D…) phụ thuộc ngược lại A. Đồ thị phụ thuộc lành mạnh phải là **DAG** (đồ thị có hướng không chu trình).

## 2. Giải thích đơn giản

Hai người bạn cùng nói: "Cậu mặc xong trước thì tớ mới mặc." Cả hai đều đợi nhau mãi.

```
Trực tiếp:      A ──► B ──► A

Gián tiếp:      A ──► B
                ▲     │
                └─ C ◄┘      A → B → C → A

Lành mạnh (DAG): A ──► B ──► C   (chỉ đi một chiều)
```

## 3. Vì sao là vấn đề

- **Lỗi khởi tạo:** hệ thống không biết tạo cái nào trước. Container DI báo lỗi "Cannot resolve dependency".
- **Giá trị `undefined` lúc chạy:** ở module system, một bên nhận được module chưa nạp xong.
- **Khó hiểu, khó test, khó tách:** không thể kiểm thử hay tái sử dụng A mà không kéo theo B (và ngược lại).
- **Dấu hiệu thiết kế sai:** hai khối đang chia sẻ trách nhiệm.

## 4. Hai nơi hay gặp

### 4.1 Giữa các module (import)

```js
// a.js
const { b } = require('./b');
exports.a = () => b();

// b.js
const { a } = require('./a');   // a.js chưa chạy xong → a là undefined
exports.b = () => a();
```

CommonJS trả về **bản export chưa hoàn chỉnh** của module đang nạp dở. ES Modules dùng live binding nên có thể vẫn chạy, nhưng gặp lỗi `ReferenceError` ở TDZ nếu dùng giá trị quá sớm.

### 4.2 Giữa các service trong DI

```ts
class OrderService {
  constructor(private payment: PaymentService) {}
}
class PaymentService {
  constructor(private order: OrderService) {}   // vòng: Order → Payment → Order
}
```

Container không thể tạo `OrderService` mà chưa có `PaymentService` và ngược lại. Xem [Dependency Injection](./dependency-injection.md).

## 5. Cách phát hiện

- Công cụ: `madge --circular src/`, ESLint rule `import/no-cycle`, `dependency-cruiser`.
- Triệu chứng: `undefined is not a function` lúc import, lỗi DI "circular dependency detected".
- Đưa kiểm tra vào CI để không tái phát.

## 6. Cách gỡ vòng

| Cách | Ý tưởng |
|---|---|
| **Tách module chung** | Phần cả A và B cùng cần thì rút ra module C. A → C và B → C, không còn vòng |
| **Đảo ngược phụ thuộc** (DIP) | Cả hai cùng phụ thuộc một **interface** nằm riêng, không phụ thuộc lẫn nhau |
| **Dùng event** | A phát event, B lắng nghe, A không cần biết B ([Queue và Event](./queue-event.md)) |
| **Truyền tham số** | Thay vì B import A, A truyền thứ B cần vào lúc gọi |
| **Gộp lại** | Nếu hai thứ luôn đi cùng nhau thì chúng có thể vốn là một |
| **Tải trễ** (lazy / `forwardRef`) | Giải pháp tạm thời, chỉ che triệu chứng |

```
Trước:   OrderService ◄──► PaymentService

Sau:     OrderService ──► PaymentPort (interface) ◄── PaymentService
                          (cả hai chỉ biết interface, không biết nhau)
```

Dùng `forwardRef` (NestJS) hay `import` bên trong hàm chỉ nên là bước đệm, vì gốc rễ là thiết kế phụ thuộc hai chiều.

## 7. Ghi chú FE và DB

> **FE:** Cũng gặp ở `import` giữa các component, store, util. Tách nhỏ file, đặt tập hợp type/hằng số vào file riêng, và bundler (webpack/Vite) cảnh báo cycle. Barrel file (`index.ts` re-export tất cả) là thủ phạm quen thuộc.

> **DB:** Phiên bản tương tự là **khóa ngoại vòng** (bảng A tham chiếu B, B tham chiếu A) khiến không insert được. Cách xử lý: để một khóa ngoại cho phép `NULL` rồi cập nhật sau, hoặc dùng `DEFERRABLE INITIALLY DEFERRED` để kiểm tra khi commit. Còn *chu trình chờ khóa* giữa transaction là [deadlock](../db/deadlock.md), một khái niệm khác.

## 8. Liên quan

- [Dependency Injection](./dependency-injection.md)
- [Deadlock](../db/deadlock.md): cũng là "vòng", nhưng là vòng chờ tài nguyên lúc chạy.
