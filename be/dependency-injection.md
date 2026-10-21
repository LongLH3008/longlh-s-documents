# Dependency Injection (DI) là gì?

## 1. Hiểu bằng ví dụ đời thường

Bạn mở quán cà phê và cần **máy pha cà phê** để làm việc.

- **Cách không dùng DI:** Bạn tự đi mua nguyên liệu, tự chế máy pha cà phê ngay trong quán. Muốn đổi sang loại máy khác thì phải đập đi làm lại.
- **Cách dùng DI:** Có người _đưa sẵn_ máy pha cà phê cho bạn. Bạn chỉ cần biết "máy này pha được cà phê" và dùng nó. Muốn đổi máy, chỉ cần đưa cái máy khác vào.

DI chính là nguyên tắc thứ hai: **một đối tượng không tự tạo ra những thứ nó cần, mà được cung cấp từ bên ngoài.**

---

## 2. Các khái niệm cơ bản

| Thuật ngữ                       | Ý nghĩa                                              | Trong ví dụ quán cà phê |
| ------------------------------- | ---------------------------------------------------- | ----------------------- |
| **Dependency** (phụ thuộc)      | Thứ mà một class cần để hoạt động                    | Máy pha cà phê          |
| **Dependent / Client**          | Class đang cần dependency                            | Quán cà phê             |
| **Injection** (tiêm/truyền vào) | Hành động cung cấp dependency từ bên ngoài           | Người đưa máy cho bạn   |
| **Injector / Container**        | Thành phần chịu trách nhiệm tạo và truyền dependency | Người giao máy          |

**Định nghĩa chuẩn:**

> **Dependency Injection** là một design pattern, trong đó các dependency của một object được **cung cấp từ bên ngoài** thay vì object tự khởi tạo chúng bên trong.

---

## 3. Ví dụ code: Không dùng DI và dùng DI

### ❌ Không dùng DI (tight coupling)

```ts
class MySQLDatabase {
	save(data: string) {
		console.log("Lưu vào MySQL:", data);
	}
}

class UserService {
	private db = new MySQLDatabase(); // tự tạo dependency

	createUser(name: string) {
		this.db.save(name);
	}
}
```

**Vấn đề:**

- `UserService` bị **dính chặt** (tightly coupled) vào `MySQLDatabase`.
- Muốn đổi sang MongoDB phải **sửa code bên trong** `UserService`.
- Khó viết **unit test**, vì không thay được database thật bằng database giả (mock).

### ✅ Dùng DI (loose coupling)

```ts
interface Database {
	save(data: string): void;
}

class MySQLDatabase implements Database {
	save(data: string) {
		console.log("Lưu vào MySQL:", data);
	}
}

class MongoDatabase implements Database {
	save(data: string) {
		console.log("Lưu vào Mongo:", data);
	}
}

class UserService {
	// dependency được truyền từ bên ngoài qua constructor
	constructor(private db: Database) {}

	createUser(name: string) {
		this.db.save(name);
	}
}

// Bên ngoài quyết định dùng loại nào
const service1 = new UserService(new MySQLDatabase());
const service2 = new UserService(new MongoDatabase());
```

`UserService` giờ chỉ biết đến **interface** `Database`, không quan tâm bên dưới là MySQL hay Mongo.

---

## 4. Ba cách inject phổ biến

1. **Constructor Injection**: truyền qua constructor (phổ biến nhất, nên dùng).
2. **Setter Injection**: truyền qua hàm setter sau khi tạo object.
3. **Interface Injection**: dependency cung cấp một method để client nhận injector (ít dùng).

---

## 5. Các khái niệm liên quan (hay bị nhầm)

- **IoC (Inversion of Control, đảo ngược quyền điều khiển):** Nguyên lý tổng quát, trong đó **quyền tạo và quản lý object được giao cho bên ngoài** (framework/container) thay vì class tự quyết định. **DI là một cách hiện thực hóa IoC.**
- **IoC Container / DI Container:** Công cụ tự động tạo object và inject dependency cho bạn. Ví dụ: NestJS, Angular, Spring.
- **Dependency Inversion Principle (DIP):** Chữ **D** trong SOLID. Phát biểu: _module cấp cao không nên phụ thuộc module cấp thấp; cả hai nên phụ thuộc vào abstraction (interface)_. DIP là **nguyên tắc thiết kế**, còn DI là **kỹ thuật** để thực hiện nó.

> Tóm tắt quan hệ: **DIP** (nguyên tắc) → **IoC** (tư tưởng) → **DI** (kỹ thuật cụ thể).

---

## 6. DI trong NestJS (bạn đang dùng)

```ts
@Injectable()
export class UserService {
	constructor(private readonly db: DatabaseService) {}
}
```

- `@Injectable()` đánh dấu class **có thể được inject**.
- NestJS có **IoC Container** tự tạo `DatabaseService` rồi truyền vào `UserService`. Bạn không cần viết `new`.
- Các class được khai báo trong `providers` của module.

---

## 7. Lợi ích của DI

- **Dễ test:** thay dependency thật bằng mock/fake.
- **Dễ bảo trì và mở rộng:** đổi implementation mà không sửa code cũ.
- **Giảm coupling:** các thành phần ít phụ thuộc lẫn nhau.
- **Tái sử dụng code** tốt hơn.

## 8. Nhược điểm cần biết

- Với dự án nhỏ, có thể thấy **hơi rườm rà**.
- Người mới khó theo dõi vì object được tạo "ngầm" bởi container.
- Lỗi cấu hình (thiếu provider) thường chỉ báo khi chạy.

---

## Tóm tắt một câu

**DI = đừng tự `new` thứ mình cần bên trong class; hãy nhận nó từ bên ngoài (thường qua constructor), và phụ thuộc vào interface thay vì class cụ thể.**

Nếu bạn muốn, mình có thể giải thích thêm DI trong **React** (Context, props) hoặc làm bài tập nhỏ với NestJS để luyện tập.
