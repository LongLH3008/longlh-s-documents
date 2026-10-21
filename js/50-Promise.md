# **Tất cả về Promise trong JavaScript**

## **1. Promise là gì?**

`Promise` là một đối tượng trong JavaScript giúp xử lý các tác vụ bất đồng bộ một cách rõ ràng hơn, tránh hiện tượng **Callback Hell**. Một `Promise` đại diện cho một giá trị sẽ có vào một thời điểm nào đó trong tương lai.

## **2. Các trạng thái của Promise**

Một `Promise` có thể có ba trạng thái:

- **Pending** (Chờ xử lý): Khi Promise mới được tạo, chưa có kết quả.
- **Fulfilled** (Hoàn thành): Khi tác vụ thành công và trả về một giá trị.
- **Rejected** (Bị từ chối): Khi có lỗi xảy ra.

## **3. Cách tạo một Promise**

Cú pháp cơ bản:

```javascript
const myPromise = new Promise((resolve, reject) => {
	// Thực hiện tác vụ bất đồng bộ
	let success = true; // Thay đổi thành false để test reject

	setTimeout(() => {
		if (success) {
			resolve("Thành công!"); // Khi thành công, gọi resolve()
		} else {
			reject("Thất bại!"); // Khi thất bại, gọi reject()
		}
	}, 2000);
});

// Sử dụng Promise
myPromise
	.then((result) => console.log("Kết quả:", result)) // Xử lý khi thành công
	.catch((error) => console.log("Lỗi:", error)) // Xử lý khi thất bại
	.finally(() => console.log("Hoàn tất!")); // Luôn chạy dù thành công hay thất bại
```

## **4. Promise Chain (Chuỗi Promise)**

Thay vì lồng callback, chúng ta có thể nối chuỗi `.then()` để xử lý tuần tự:

```javascript
function step1() {
	return new Promise((resolve) => {
		setTimeout(() => {
			console.log("Bước 1");
			resolve();
		}, 1000);
	});
}

function step2() {
	return new Promise((resolve) => {
		setTimeout(() => {
			console.log("Bước 2");
			resolve();
		}, 1000);
	});
}

function step3() {
	return new Promise((resolve) => {
		setTimeout(() => {
			console.log("Bước 3");
			resolve();
		}, 1000);
	});
}

// Gọi tuần tự
step1()
	.then(() => step2())
	.then(() => step3())
	.then(() => console.log("Hoàn thành!"));
```

## **5. Xử lý nhiều Promise cùng lúc**

### **Promise.all()** – Chạy song song, trả về tất cả kết quả khi thành công

```javascript
const p1 = new Promise((resolve) => setTimeout(() => resolve("A"), 1000));
const p2 = new Promise((resolve) => setTimeout(() => resolve("B"), 2000));
const p3 = new Promise((resolve) => setTimeout(() => resolve("C"), 3000));

Promise.all([p1, p2, p3]).then((values) => {
	console.log("Tất cả hoàn thành:", values);
});
```

💡 **Lưu ý:** Nếu **một Promise bị lỗi**, toàn bộ `Promise.all()` sẽ bị lỗi.

---

### **Promise.allSettled()** – Chạy song song, trả về kết quả từng Promise dù có lỗi

```javascript
const p1 = new Promise((resolve) => setTimeout(() => resolve("A"), 1000));
const p2 = new Promise((_, reject) => setTimeout(() => reject("Lỗi B"), 2000));
const p3 = new Promise((resolve) => setTimeout(() => resolve("C"), 3000));

Promise.allSettled([p1, p2, p3]).then((results) => {
	console.log(results);
});
```

➡ **Kết quả sẽ hiển thị cả Promise thành công và thất bại.**

---

### **Promise.race()** – Trả về kết quả Promise chạy nhanh nhất

```javascript
const p1 = new Promise((resolve) => setTimeout(() => resolve("A"), 3000));
const p2 = new Promise((resolve) => setTimeout(() => resolve("B"), 2000));
const p3 = new Promise((resolve) => setTimeout(() => resolve("C"), 1000));

Promise.race([p1, p2, p3]).then((value) => {
	console.log("Promise chạy nhanh nhất:", value);
});
```

💡 **Lưu ý:** Nếu Promise nhanh nhất bị lỗi, `Promise.race()` cũng sẽ bị lỗi.

---

### **Promise.any()** – Trả về Promise thành công nhanh nhất

```javascript
const p1 = new Promise((_, reject) => setTimeout(() => reject("Lỗi A"), 1000));
const p2 = new Promise((resolve) => setTimeout(() => resolve("B"), 2000));
const p3 = new Promise((resolve) => setTimeout(() => resolve("C"), 3000));

Promise.any([p1, p2, p3]).then((value) => {
	console.log("Promise thành công nhanh nhất:", value);
});
```

💡 **Lưu ý:** Nếu tất cả các Promise đều thất bại, `Promise.any()` sẽ trả về lỗi tập hợp.

---

## **6. Sử dụng Async/Await để thay thế Promise**

Code với **async/await** dễ đọc hơn, giống như code đồng bộ:

```javascript
async function run() {
	try {
		console.log("Bắt đầu...");
		let result1 = await step1();
		let result2 = await step2();
		let result3 = await step3();
		console.log("Hoàn thành!");
	} catch (error) {
		console.log("Lỗi:", error);
	}
}

run();
```

💡 **Lưu ý:** `await` chỉ hoạt động bên trong **async function**.

---

## **7. Kết luận**

- **Promise** giúp xử lý bất đồng bộ tốt hơn callback.
- Sử dụng **Promise chaining** để tránh Callback Hell.
- Dùng **Promise.all(), Promise.race(), Promise.any()** để tối ưu hiệu suất.
- **Async/Await** giúp code dễ đọc hơn, thay thế `then/catch` trong Promise.

👉 **Học Promise giúp bạn làm việc với API, dữ liệu bất đồng bộ và cải thiện hiệu suất trong JavaScript.**
