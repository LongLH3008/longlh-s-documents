# Callback

### **Tất cả về Callback trong JavaScript**

#### **1. Callback là gì?**

Callback là một hàm được truyền vào một hàm khác như một đối số và được gọi lại (callback) sau khi hàm cha thực hiện xong. Callback thường được sử dụng trong các tác vụ bất đồng bộ như xử lý sự kiện, đọc file, hoặc gọi API.

#### **2. Cách sử dụng Callback**

Ví dụ đơn giản về callback trong JavaScript:

```javascript
function greet(name, callback) {
	console.log("Xin chào, " + name);
	callback();
}

function sayGoodbye() {
	console.log("Tạm biệt!");
}

greet("Nam", sayGoodbye);
// Output:
// Xin chào, Nam
// Tạm biệt!
```

#### **3. Callback trong các hàm bất đồng bộ**

Callback thường được sử dụng trong các tác vụ bất đồng bộ như `setTimeout()`, `setInterval()`, hoặc khi làm việc với API.

Ví dụ với `setTimeout()`:

```javascript
setTimeout(() => {
	console.log("Đã chờ 2 giây!");
}, 2000);
```

Ví dụ với `readFile()` trong Node.js:

```javascript
const fs = require("fs");

fs.readFile("file.txt", "utf8", (err, data) => {
	if (err) {
		console.error("Lỗi đọc file:", err);
		return;
	}
	console.log("Nội dung file:", data);
});
```

#### **4. Callback Hell (Địa ngục Callback)**

Khi có nhiều callback lồng nhau, code sẽ trở nên khó đọc và khó bảo trì, gọi là **Callback Hell**.

Ví dụ Callback Hell:

```javascript
function step1(callback) {
	setTimeout(() => {
		console.log("Bước 1");
		callback();
	}, 1000);
}

function step2(callback) {
	setTimeout(() => {
		console.log("Bước 2");
		callback();
	}, 1000);
}

function step3(callback) {
	setTimeout(() => {
		console.log("Bước 3");
		callback();
	}, 1000);
}

// Gọi callback lồng nhau
step1(() => {
	step2(() => {
		step3(() => {
			console.log("Hoàn thành!");
		});
	});
});
```

💡 **Cách khắc phục**: Sử dụng **Promise** hoặc **async/await** để thay thế callback lồng nhau.

#### **5. Thay thế Callback bằng Promise**

Sử dụng `Promise` để cải thiện code:

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

step1()
	.then(() => step2())
	.then(() => step3())
	.then(() => console.log("Hoàn thành!"));
```

#### **6. Sử dụng async/await để thay thế Callback**

Code với `async/await` dễ đọc hơn:

```javascript
async function processSteps() {
	await step1();
	await step2();
	await step3();
	console.log("Hoàn thành!");
}
processSteps();
```

### **Kết luận**

- **Callback** giúp xử lý bất đồng bộ nhưng có thể dẫn đến **Callback Hell**.
- **Promise** và **async/await** giúp code dễ đọc hơn, tránh Callback Hell.
- Trong các dự án hiện đại, **async/await** được khuyến khích sử dụng thay vì callback truyền thống.s
