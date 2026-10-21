# Debounce

Chức năng `debounce` là một công cụ thường được sử dụng trong JavaScript để giới hạn tần suất thực thi của một hàm. Nó đảm bảo rằng hàm chỉ được gọi sau một khoảng thời gian không có sự hoạt động kể từ lần gọi cuối cùng. Điều này rất hữu ích để tối ưu hóa hiệu suất trong các tình huống như xử lý sự kiện nhập dữ liệu của người dùng, thay đổi kích thước cửa sổ, hoặc gọi API trong khi gõ tìm kiếm.

### Giải thích Cách Hoạt Động của Debounce:

1. **Tham Số và Cài Đặt**:

      - Hàm `debounce` nhận hai tham số:
           - `func`: Hàm bạn muốn debounce (tức là trì hoãn thực thi của nó).
           - `timeout`: Thời gian (tính bằng milliseconds) để chờ sau lần gọi cuối cùng trước khi gọi `func`. Mặc định là 300 milliseconds.

2. **Biến Timer Nội Bộ**:

      - Một biến `timer` được khai báo để theo dõi thời gian chờ hiện tại. Biến này được đặt lại mỗi khi hàm debounced được gọi lại trong khoảng thời gian timeout.

3. **Trả Về Một Hàm**:

      - Hàm `debounce` trả về một hàm mới bao bọc hàm `func`.
      - Hàm trả về này là hàm sẽ được gọi bất cứ khi nào bạn muốn debounce `func`.

4. **Xử Lý Các Lần Gọi Hàm**:

      - Mỗi lần hàm trả về được gọi, điều sau đây xảy ra:
           - `clearTimeout(timer)` được gọi để xóa bất kỳ timeout nào đã được thiết lập trước đó. Điều này đảm bảo rằng hàm (`func`) sẽ không được gọi cho đến khi thời gian timeout trôi qua mà không có bất kỳ lần gọi mới nào.
           - `setTimeout` sau đó được gọi để thiết lập một timeout mới. Nếu timeout này hoàn tất mà không bị gián đoạn (tức là không có lần gọi mới trong khoảng thời gian `timeout`), `func` sẽ được thực thi.

5. **Thực Thi Hàm**:
      - Khi timeout hoàn tất mà không bị xóa (tức là không có lần gọi hàm mới), dòng `func.apply(this, args)` sẽ được thực thi. Dòng này gọi hàm gốc `func` với các tham số đã cung cấp (`args`).

### Ví Dụ Thực Tế:

Xem xét ví dụ sau:

```javascript
function debounce(func, timeout = 300) {
	let timer;
	return (...args) => {
		clearTimeout(timer);
		timer = setTimeout(() => {
			func.apply(this, args);
		}, timeout);
	};
}

function saveInput() {
	console.log("Saving data");
}

const processChange = debounce(() => saveInput());

// Mô phỏng nhiều lần gọi processChange
processChange(); // gọi 1
processChange(); // gọi 2 trong vòng 300 ms
processChange(); // gọi 3 trong vòng 300 ms
```

**Giải Thích Từng Bước**:

1. **Lần Gọi Đầu Tiên đến `processChange`**:

      - `clearTimeout(timer)` được gọi, nhưng `timer` là `undefined` (vì đây là lần gọi đầu tiên).
      - `setTimeout` thiết lập một thời gian chờ để gọi `saveInput` sau 300ms.
      - `timer` được cập nhật để tham chiếu đến timeout mới này.

2. **Các Lần Gọi Tiếp Theo đến `processChange` (Trong 300ms)**:

      - Mỗi lần gọi đều xóa timeout trước đó bằng `clearTimeout(timer)`.
      - Một timeout mới được thiết lập để gọi `saveInput` sau 300ms.
      - `timer` được cập nhật để tham chiếu đến timeout mới mỗi lần.

3. **Sau 300ms Không Hoạt Động**:
      - Nếu không có lần gọi mới đến `processChange` trong 300ms, timeout hoàn tất.
      - Hàm `saveInput` được gọi, và "Saving data" sẽ được ghi vào console.

### Điểm Quan Trọng:

- **Debounce Trì Hoãn Thực Thi**: Mục tiêu chính của debounce là trì hoãn việc thực thi một hàm cho đến khi một khoảng thời gian nhất định đã trôi qua mà không có thêm lần gọi mới.
- **Ngăn Chặn Các Lần Gọi Quá Mức**: Điều này rất hữu ích trong các tình huống mà bạn muốn tránh nhiều lần thực thi hàm trong một khoảng thời gian ngắn, như sự kiện nhập dữ liệu của người dùng hoặc thay đổi kích thước cửa sổ.
- **Tối Ưu Hiệu Suất**: Bằng cách giảm số lượng lần gọi hàm, debounce có thể cải thiện hiệu suất và giảm tải không cần thiết trên trình duyệt hoặc máy chủ.
