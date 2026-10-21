# String

Dưới đây là một số phương thức thường được sử dụng cho chuỗi (string methods) trong JavaScript:

### 1. **`concat(str1, str2, ..., strN)`:**
   - Nối các chuỗi lại với nhau.
   ```javascript
   const str1 = 'Hello';
   const str2 = ' ';
   const str3 = 'World';
   const result = str1.concat(str2, str3);
   console.log(result); // Output: 'Hello World'
   ```

### 2. **`toUpperCase()`:**
   - Chuyển đổi chuỗi thành chữ hoa.
   ```javascript
   const lowercaseStr = 'hello';
   const uppercaseStr = lowercaseStr.toUpperCase();
   console.log(uppercaseStr); // Output: 'HELLO'
   ```

### 3. **`toLowerCase()`:**
   - Chuyển đổi chuỗi thành chữ thường.
   ```javascript
   const uppercaseStr = 'HELLO';
   const lowercaseStr = uppercaseStr.toLowerCase();
   console.log(lowercaseStr); // Output: 'hello'
   ```

### 4. **`indexOf(searchString, position)`:**
   - Trả về index của lần xuất hiện đầu tiên của chuỗi tìm kiếm, hoặc -1 nếu không tìm thấy.
   ```javascript
   const sentence = 'This is a sample sentence.';
   const index = sentence.indexOf('sample');
   console.log(index); // Output: 10
   ```

### 5. **`slice(start, end)`:**
   - Trả về một phần của chuỗi từ index `start` đến `end` (không bao gồm `end`).
   ```javascript
   const text = 'JavaScript';
   const sliced = text.slice(0, 4);
   console.log(sliced); // Output: 'Java'
   ```

### 6. **`replace(searchValue, newValue)`:**
   - Thay thế chuỗi con đầu tiên tìm thấy bằng giá trị mới.
   ```javascript
   const message = 'Hello, World!';
   const newMessage = message.replace('World', 'Universe');
   console.log(newMessage); // Output: 'Hello, Universe!'
   ```

### 7. **`split(separator, limit)`:**
   - Chia chuỗi thành một mảng các chuỗi con dựa trên separator.
   ```javascript
   const sentence = 'This is a sample sentence.';
   const words = sentence.split(' ');
   console.log(words); // Output: ['This', 'is', 'a', 'sample', 'sentence.']
   ```

### 8. **`charAt(index)`:**
   - Trả về ký tự ở vị trí index trong chuỗi.
   ```javascript
   const str = 'Hello';
   const char = str.charAt(1);
   console.log(char); // Output: 'e'
   ```

### 9. **`charCodeAt(index)`:**
   - Trả về mã Unicode của ký tự ở vị trí index trong chuỗi.
   ```javascript
   const str = 'Hello';
   const unicode = str.charCodeAt(1);
   console.log(unicode); // Output: 101
   ```

### 10. **`startsWith(searchString, position)`:**
   - Kiểm tra xem chuỗi có bắt đầu bằng chuỗi tìm kiếm hay không.
   ```javascript
   const str = 'Hello, World!';
   const startsWithHello = str.startsWith('Hello');
   console.log(startsWithHello); // Output: true
   ```

### 11. **`endsWith(searchString, position)`:**
   - Kiểm tra xem chuỗi có kết thúc bằng chuỗi tìm kiếm hay không.
   ```javascript
   const str = 'Hello, World!';
   const endsWithWorld = str.endsWith('World!');
   console.log(endsWithWorld); // Output: true
   ```

### 12. **`trim()`:**
   - Loại bỏ khoảng trắng ở đầu và cuối chuỗi.
   ```javascript
   const paddedStr = '   Hello, World!   ';
   const trimmedStr = paddedStr.trim();
   console.log(trimmedStr); // Output: 'Hello, World!'
   ```

### 13. **`repeat(count)`:**
   - Lặp lại chuỗi một số lần được xác định bởi tham số count.
   ```javascript
   const str = 'Hello';
   const repeatedStr = str.repeat(3);
   console.log(repeatedStr); // Output: 'HelloHelloHello'
   ```

### 14. **`includes(searchString, position)`:**
   - Kiểm tra xem chuỗi có chứa chuỗi tìm kiếm hay không và trả về giá trị boolean.
   ```javascript
   const str = 'Hello, World!';
   const includesHello = str.includes('Hello');
   console.log(includesHello); // Output: true
   ```

### 15. **`endsWith(searchString, position)`:**
   - Kiểm tra xem chuỗi có kết thúc bằng chuỗi tìm kiếm hay không.
   ```javascript
   const str = 'Hello, World!';
   const endsWithExclamation = str.endsWith('!');
   console.log(endsWithExclamation); // Output: true
   ```

### 16. **`padStart(targetLength, padString)`, `padEnd(targetLength, padString)`:**
   - Thêm ký tự bằng padString vào đầu (padStart) hoặc cuối (padEnd) chuỗi để đạt được độ dài mong muốn.
   ```javascript
   const str = 'Hello';
   const paddedStartStr = str.padStart(8, '*');
   console.log(paddedStartStr); // Output: '***Hello'
   
   const paddedEndStr = str.padEnd(8, '*');
   console.log(paddedEndStr); // Output: 'Hello***'
   ```

### 17. **`substring(startIndex, endIndex)`:**
   - Trả về một phần con của chuỗi từ startIndex đến endIndex (không bao gồm endIndex).
   ```javascript
   const str = 'Hello, World!';
   const subString = str.substring(7, 12);
   console.log(subString); // Output: 'World'
   ```

### 18. **`toLocaleUpperCase([locale])`, `toLocaleLowerCase([locale])`:**
   - Chuyển đổi chuỗi thành chữ hoa (toLocaleUpperCase) hoặc chữ thường (toLocaleLowerCase) dựa trên locale hiện tại.
   ```javascript
   const str = 'Hello, World!';
   const upperCaseStr = str.toLocaleUpperCase('en-US');
   console.log(upperCaseStr); // Output: 'HELLO, WORLD!'
   ```

### 19. **`trimStart()`, `trimEnd()`:**
   - Loại bỏ khoảng trắng ở đầu (trimStart) hoặc cuối (trimEnd) chuỗi.
   ```javascript
   const paddedStr = '   Hello, World!   ';
   const trimmedStartStr = paddedStr.trimStart();
   const trimmedEndStr = paddedStr.trimEnd();
   console.log(trimmedStartStr); // Output: 'Hello, World!   '
   console.log(trimmedEndStr); // Output: '   Hello, World!'
   ```

Dưới đây là một số phương thức tiếp theo cho chuỗi mà bạn có thể quan tâm:

### 20. **`match(regexp)`:**
   - Sử dụng biểu thức chính quy để trả về một mảng các kết quả phù hợp.
   ```javascript
   const text = 'Hello, World!';
   const matches = text.match(/o/g);
   console.log(matches); // Output: ['o', 'o']
   ```

### 21. **`search(regexp)`:**
   - Tìm kiếm chuỗi theo biểu thức chính quy và trả về vị trí của kết quả đầu tiên hoặc -1 nếu không tìm thấy.
   ```javascript
   const text = 'Hello, World!';
   const position = text.search(/World/);
   console.log(position); // Output: 7
   ```

### 22. **`replaceAll(searchValue, replaceValue)`:**
   - Thay thế tất cả các xuất hiện của chuỗi tìm kiếm bằng giá trị thay thế mới.
   ```javascript
   const sentence = 'The quick brown fox jumps over the lazy dog.';
   const newSentence = sentence.replaceAll('fox', 'cat');
   console.log(newSentence); // Output: 'The quick brown cat jumps over the lazy dog.'
   ```

### 23. **`normalize([form])`:**
   - Chuẩn hóa chuỗi Unicode bằng cách chuyển đổi các ký tự có dấu thành ký tự không dấu.
   ```javascript
   const accentedStr = 'Café';
   const normalizedStr = accentedStr.normalize();
   console.log(normalizedStr); // Output: 'Cafe'
   ```

### 24. **`valueOf()`, `toString()`:**
   - Trả về giá trị nguyên thủy của chuỗi (valueOf) hoặc biểu diễn chuỗi dưới dạng chuỗi (toString).
   ```javascript
   const str = 'Hello, World!';
   const value = str.valueOf();
   const strToString = str.toString();
   console.log(value); // Output: 'Hello, World!'
   console.log(strToString); // Output: 'Hello, World!'
   ```

### 25. **`toLocaleString([locales [, options]])`:**
   - Trả về chuỗi biểu diễn của đối tượng theo ngôn ngữ và các tùy chọn cụ thể cho ngôn ngữ đó.
   ```javascript
   const number = 123456.789;
   const formattedNumber = number.toLocaleString('en-US');
   console.log(formattedNumber); // Output: '123,456.789'
   ```
