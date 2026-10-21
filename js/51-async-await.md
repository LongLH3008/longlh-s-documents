# **Tất cả về async/await trong JavaScript**

## **1. Async/Await là gì?**

`async/await` là một cú pháp mới của JavaScript giúp xử lý các tác vụ **bất đồng bộ (asynchronous)** một cách dễ đọc và dễ hiểu hơn so với sử dụng `Promise` thuần hoặc `callback`.

**✅ Ưu điểm của `async/await`**:

- Viết code gọn gàng, dễ đọc, giống như code đồng bộ.
- Tránh được **Callback Hell** và **Promise chaining**.
- Xử lý lỗi dễ dàng với `try/catch`.

---

## **2. Cách sử dụng async/await**

### **2.1. Khai báo async function**

Bất kỳ hàm nào khai báo với `async` đều sẽ **trả về một Promise**.

```javascript
async function myFunction() {
	return "Hello World";
}

myFunction().then(console.log); // "Hello World"
```

🚀 **Lưu ý:** Hàm `async` luôn trả về một `Promise`, nếu không dùng `return`, nó sẽ trả về `Promise.resolve(undefined)`.

---

### **2.2. Sử dụng await**

`await` giúp **tạm dừng** thực thi hàm `async` cho đến khi `Promise` hoàn thành.

```javascript
function delay(ms) {
	return new Promise((resolve) => setTimeout(resolve, ms));
}

async function demo() {
	console.log("Bắt đầu...");
	await delay(2000); // Chờ 2 giây
	console.log("Hoàn thành!");
}

demo();
```

✅ **Ưu điểm**:

- Giúp code đọc theo dòng từ trên xuống, không cần `.then()`.
- Không bị lồng nhau như callback.

---

## **3. Xử lý lỗi với try/catch**

Khi sử dụng `async/await`, có thể dùng `try/catch` để bắt lỗi dễ dàng hơn:

```javascript
async function fetchData() {
	try {
		let response = await fetch("https://jsonplaceholder.typicode.com/posts/1");
		let data = await response.json();
		console.log("Dữ liệu nhận được:", data);
	} catch (error) {
		console.log("Lỗi xảy ra:", error);
	}
}

fetchData();
```

✅ **Lợi ích:** Không cần `.catch()` như Promise, dễ dàng kiểm soát lỗi.

---

## **4. Xử lý nhiều Promise với async/await**

### **4.1. Chạy tuần tự (Sequential)**

Nếu có nhiều Promise cần chạy **tuần tự**, dùng `await` cho từng Promise:

```javascript
async function processSteps() {
	console.log("Bước 1...");
	await delay(1000);

	console.log("Bước 2...");
	await delay(1000);

	console.log("Bước 3...");
	await delay(1000);

	console.log("Hoàn thành!");
}

processSteps();
```

⏳ **Nhược điểm**: Chạy tuần tự có thể mất nhiều thời gian.

---

### **4.2. Chạy song song (Parallel)**

Dùng `Promise.all()` để chạy nhiều Promise **cùng lúc**, giảm thời gian chờ:

```javascript
async function fetchAllData() {
	let userPromise = fetch("https://jsonplaceholder.typicode.com/users/1").then((res) => res.json());
	let postPromise = fetch("https://jsonplaceholder.typicode.com/posts/1").then((res) => res.json());

	let [user, post] = await Promise.all([userPromise, postPromise]);

	console.log("User:", user);
	console.log("Post:", post);
}

fetchAllData();
```

🔥 **Ưu điểm:** `Promise.all()` giúp chạy song song nhanh hơn so với chạy tuần tự.

---

## **5. Sử dụng async/await trong vòng lặp**

Khi cần chạy tuần tự trong vòng lặp, dùng `await` trong vòng lặp `for-of`:

```javascript
const tasks = [1000, 2000, 1500];

async function processTasks() {
	for (const time of tasks) {
		console.log(`Đang xử lý trong ${time}ms...`);
		await delay(time);
	}
	console.log("Tất cả hoàn thành!");
}

processTasks();
```

⛔ **Lưu ý:** **Không dùng `forEach()` với `await`** vì `forEach()` không hỗ trợ async.

---

## **6. Xử lý lỗi cho nhiều Promise**

Dùng `Promise.allSettled()` để nhận kết quả của **tất cả Promise**, dù có lỗi:

```javascript
async function fetchMultiple() {
	let results = await Promise.allSettled([
		fetch("https://jsonplaceholder.typicode.com/users/1").then((res) => res.json()),
		fetch("https://jsonplaceholder.typicode.com/posts/1").then((res) => res.json()),
		fetch("https://invalid-url.com").then((res) => res.json()), // Lỗi
	]);

	console.log(results);
}

fetchMultiple();
```

✅ **Lợi ích:** Không bị lỗi toàn bộ nếu có một Promise thất bại.

---

## **7. So sánh async/await và Promise**

| **Thuộc tính**     | **Promise**                        | **Async/Await**                  |
| ------------------ | ---------------------------------- | -------------------------------- |
| **Cú pháp**        | `.then()` / `.catch()`             | `await` và `try/catch`           |
| **Đọc code**       | Dễ gây Callback Hell nếu lồng nhau | Code dễ đọc hơn                  |
| **Bắt lỗi**        | `.catch()`                         | Dùng `try/catch`, dễ quản lý hơn |
| **Chạy song song** | Dùng `Promise.all()`               | Dùng `Promise.all()`             |

---

## **8. Kết luận**

- `async/await` giúp viết code bất đồng bộ **dễ đọc hơn Promise**.
- `await` giúp xử lý tuần tự **một cách rõ ràng**, nhưng cần dùng `Promise.all()` để tối ưu hiệu suất.
- **Luôn dùng `try/catch`** để xử lý lỗi khi dùng `await`.

💡 **Học `async/await` giúp bạn làm việc hiệu quả hơn với API, dữ liệu và xử lý bất đồng bộ trong JavaScript!** 🚀
