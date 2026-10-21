# SSE và Streaming

## 1. Định nghĩa kỹ thuật

**Server-Sent Events (SSE)** là một đặc tả trong HTML Living Standard cho phép server **đẩy dữ liệu một chiều** (server → client) qua **một kết nối HTTP duy nhất, mở lâu**. Server trả về `Content-Type: text/event-stream`, client dùng API `EventSource`.

**Streaming** (response streaming) là kỹ thuật gửi response **từng phần ngay khi có**, thay vì chờ tạo xong toàn bộ rồi gửi một lần. Ở HTTP/1.1 dùng `Transfer-Encoding: chunked`, ở HTTP/2 dùng các DATA frame.

## 2. Giải thích đơn giản

- **Cách thường:** bạn gọi món, đầu bếp nấu xong cả mâm rồi mới bưng ra. Bạn đứng chờ.
- **Streaming:** món nào xong bưng món đó. Bạn bắt đầu ăn ngay.
- **SSE:** bạn ngồi yên, nhà hàng **chủ động báo** mỗi khi có món mới hoặc thông báo, bạn không phải hỏi lại liên tục.

```
Polling:   client ─?─► server   (có gì mới không?)   ← lặp liên tục, phí request
           client ─?─► server   (không)
SSE:       client ─────────────► server  (mở 1 kết nối)
           client ◄── event ──── server
           client ◄── event ──── server   ← server đẩy khi có dữ liệu
```

## 3. Định dạng SSE

```
HTTP/1.1 200 OK
Content-Type: text/event-stream
Cache-Control: no-cache
Connection: keep-alive

id: 101
event: price
data: {"symbol":"BTC","price":64000}

: đây là comment, dùng làm heartbeat

data: dòng 1
data: dòng 2

```

Mỗi event kết thúc bằng **một dòng trống**. Các trường: `data`, `event` (tên sự kiện), `id` (dùng để nối lại), `retry` (ms chờ trước khi nối lại).

**Tự động nối lại:** nếu đứt kết nối, trình duyệt tự kết nối lại và gửi header `Last-Event-ID: 101`. Server dựa vào đó phát bù các event bị lỡ.

## 4. Code

```ts
// BE (Express)
app.get('/events', (req, res) => {
  res.set({
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    Connection: 'keep-alive',
    'X-Accel-Buffering': 'no',          // tắt buffer của nginx
  });
  res.flushHeaders();

  const timer = setInterval(() => res.write(`data: ${Date.now()}\n\n`), 1000);
  const ping = setInterval(() => res.write(': ping\n\n'), 15000); // heartbeat

  req.on('close', () => { clearInterval(timer); clearInterval(ping); });
});
```

```ts
// FE
const es = new EventSource('/events', { withCredentials: true });
es.onmessage = (e) => console.log(e.data);
es.addEventListener('price', (e) => render(JSON.parse(e.data)));
es.onerror = () => { /* trình duyệt tự nối lại */ };
// es.close() khi không cần nữa
```

## 5. Streaming response (ví dụ: chat AI)

`EventSource` chỉ hỗ trợ `GET`, không gửi body. Khi cần `POST` (gửi prompt), dùng `fetch` đọc stream:

```ts
const res = await fetch('/chat', { method: 'POST', body: JSON.stringify({ prompt }) });
const reader = res.body.getReader();
const decoder = new TextDecoder();
for (;;) {
  const { done, value } = await reader.read();
  if (done) break;
  appendToUI(decoder.decode(value, { stream: true }));   // hiện từng đoạn
}
```

## 6. So sánh với các cách khác

| | Short polling | Long polling | **SSE** | WebSocket |
|---|---|---|---|---|
| Hướng | Client hỏi | Client hỏi (treo chờ) | **Server → client** | Hai chiều |
| Giao thức | HTTP | HTTP | **HTTP** | Nâng cấp lên WS |
| Tự nối lại | Tự viết | Tự viết | **Có sẵn** | Tự viết |
| Qua proxy/firewall | Dễ | Dễ | **Dễ** | Đôi khi bị chặn |
| Dùng khi | Dữ liệu ít đổi | Thông báo thưa | Feed, tiến trình, thông báo, AI stream | Chat, game, cộng tác |

Chỉ cần server đẩy một chiều thì SSE đơn giản hơn WebSocket rất nhiều.

## 7. Cạm bẫy

- **Proxy buffering:** nginx/CDN gom response lại rồi mới gửi, làm streaming "không chạy". Cần tắt buffering (`X-Accel-Buffering: no`, `proxy_buffering off`).
- **Giới hạn kết nối HTTP/1.1:** trình duyệt chỉ cho ~6 kết nối mỗi domain, nhiều tab SSE sẽ chiếm hết. HTTP/2 giải quyết bằng multiplexing.
- **Kết nối treo mãi:** mỗi client giữ một socket, nên cần giới hạn số kết nối, gửi heartbeat để phát hiện kết nối chết.
- **Scale nhiều server:** event phát ở server A nhưng client nối server B. Cần kênh trung gian (Redis Pub/Sub, [queue/event](./queue-event.md)).
- **Xác thực:** `EventSource` không gửi được header tùy ý, nên dùng cookie ([opaque token](./auth-opaque-token-sliding-session.md)) hoặc `fetch` stream.

## 8. Ghi chú FE và DB

> **FE:** Cập nhật UI theo từng chunk có thể gây render dày đặc, hãy gom theo `requestAnimationFrame` hoặc [throttle](../fe/debounce-throttle.md). Xử lý parse nặng thì chuyển sang Web Worker để không chặn luồng chính.

> **DB:** Đừng polling DB liên tục cho từng client SSE. Dùng cơ chế thông báo (`LISTEN/NOTIFY` của PostgreSQL, change stream) hoặc đẩy qua queue.

## 9. Liên quan

- [Queue và Event](./queue-event.md): nguồn event để đẩy tới client.
- [Background worker](./background-worker.md): tác vụ nặng chạy nền, báo tiến trình qua SSE.
- [Upload file lớn](./upload-heavy-file.md): báo tiến trình xử lý sau upload.
