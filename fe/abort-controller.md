# AbortController

Dưới đây là tổng hợp **tóm tắt & rõ ràng** về `AbortController` — định nghĩa, cách dùng và ví dụ minh họa, hỗ trợ React/Node/static:

---

## ✅ 1. Định nghĩa & phần tử chính

- `AbortController` là một API (Web và Node.js từ v15 trở lên) cho phép **hủy (abort)** các tác vụ bất đồng bộ, như `fetch`, event listener, stream hoặc timer. Nó hoạt động cùng với `AbortSignal` để truyền tín hiệu hủy ([MDN Web Docs][1]).
- Khi gọi `controller.abort([reason])`:

     - `signal.aborted` trở thành `true`, `signal.reason` lưu lý do hủy ([LogRocket Blog][2]).
     - Các API "listening" sẽ hành động (ví dụ fetch bị reject, listener bị loại khỏi DOM…) ([LogRocket Blog][2], [Stack Overflow][3]).

---

## 🛠️ 2. Các use case phổ biến

### a) **Hủy HTTP request (Fetch API)**

```js
const controller = new AbortController();
fetch(url, { signal: controller.signal }).catch((err) => {
	if (err.name === "AbortError") console.log("Fetch aborted");
});
controller.abort();
```

Dùng cho hủy request khi user bỏ trang, tìm kiếm, quay lại, v.v ([GreatFrontEnd][4]).

### b) **Cleanup event listener**

```js
const controller = new AbortController();
el.addEventListener("scroll", onScroll, { signal: controller.signal });
el.addEventListener("click", onClick, { signal: controller.signal });
// Sau cùng:
controller.abort(); // Loại bỏ cùng lúc mọi listener đã attach
```

Tránh phải gọi `removeEventListener` từng cái ([kettanaito.com][5]).

### c) **Cancel timer, stream, hoặc custom Promise**

- ` AbortSignal.timeout(ms)` tự abort sau thời gian
- Dùng `signal.addEventListener('abort', ...)` để clear `setTimeout` hoặc cancel stream ([kettanaito.com][5]).

### d) **Kết hợp nhiều signal** — `AbortSignal.any([...signals])`

- Dừng hành động nếu bất kỳ signal nào gọi abort ([kettanaito.com][5], [Better Stack][6]).

### e) **Node.js support**

- Node >= v15 hỗ trợ `AbortSignal` trong `fetch`, `fs.readFile`, `timersPromises.setTimeout`, v.v. ([LogRocket Blog][2]).

---

## ⚙️ 3. Ví dụ minh họa

### Ví dụ React + fetch:

```tsx
useEffect(() => {
  const controller = new AbortController();
  const { signal } = controller;
  fetch('/api/data', { signal })
    .then(...).catch(err => {
      if (err.name === 'AbortError') console.log('Fetch canceled');
    });
  return () => controller.abort();
}, [url]);
```

Cleanup gọn nhẹ — abort fetch khi component unmount ([Medium][7], [LocalCan][8]).

### Ví dụ event listener:

```js
const controller = new AbortController();
window.addEventListener("resize", onResize, { signal: controller.signal });
window.addEventListener("hashchange", onHashChange, { signal });
return () => controller.abort();
```

Tự động remove tất cả listener ([kettanaito.com][5], [Medium][9]).

---

## 🧠 4. Tổng kết nhanh

| Thành phần              | Chức năng                     |
| ----------------------- | ----------------------------- |
| `new AbortController()` | Tạo controller và signal      |
| `signal`                | Đối tượng báo hiệu abort      |
| `controller.abort()`    | Kích hoạt signal.abort = true |
| `signal.aborted`        | Kiểm tra trạng thái abort     |
| `signal.reason`         | Lý do bị abort (nếu có)       |

---

## 🎯 5. Khi nào nên dùng?

- ✅ Fetch request cần hủy khi user điều hướng hoặc có request mới
- ✅ Event listener cần gỡ sạch khi component unmount
- ✅ Timer, stream, hoặc custom API cần cancel theo logic khi có abort
- ✅ Kết hợp nhiều nguồn signal (timeout hoặc user abort)
- ✅ Node.js async tasks như `fs.readFile` hoặc `timersPromises`

---

### 📚 Tài liệu tham khảo

- MDN Web Docs — `AbortController` & `abort()` method ([MDN Web Docs][1], [LocalCan][8])
- LogRocket blog — hướng dẫn chi tiết frontend/backend, React, Node.js ([LogRocket Blog][2])
- Bài viết "Don’t Sleep on AbortController" — ví dụ event listener & stream ([kettanaito.com][5])
- Medium.com – giải thích rõ ràng ai mới cũng hiểu ([Medium][7], [Medium][9])

---

[1]: https://developer.mozilla.org/en-US/docs/Web/API/AbortController?utm_source=chatgpt.com "AbortController - Web APIs | MDN"
[2]: https://blog.logrocket.com/complete-guide-abortcontroller/?utm_source=chatgpt.com "The complete guide to the AbortController API"
[3]: https://stackoverflow.com/questions/74466360/how-does-abortcontroller-work-under-the-hood?utm_source=chatgpt.com "How does AbortController work under the hood?"
[4]: https://www.greatfrontend.com/questions/quiz/how-do-you-abort-a-web-request-using-abortcontrollers?utm_source=chatgpt.com "How do you abort a web request using `AbortController` in ..."
[5]: https://kettanaito.com/blog/dont-sleep-on-abort-controller?utm_source=chatgpt.com "Don't Sleep on AbortController"
[6]: https://betterstack.com/community/guides/scaling-nodejs/understanding-abortcontroller/?utm_source=chatgpt.com "Understanding AbortController in Node.js: A Complete Guide"
[7]: https://medium.com/codex/understanding-abortcontroller-optimizing-data-fetching-67b84e285f20?utm_source=chatgpt.com "Understanding AbortController: Optimizing Data Fetching"
[8]: https://www.localcan.com/blog/abortcontroller-nodejs-react-complete-guide-examples?utm_source=chatgpt.com "AbortController in Node.js and React - Complete Guide ..."
[9]: https://aniksharif.medium.com/abortcontroller-the-javascript-feature-i-wish-i-knew-sooner-b298d16a6219?utm_source=chatgpt.com "AbortController: The JavaScript Feature I Wish I Knew Sooner"
