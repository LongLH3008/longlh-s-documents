# Scope

## Scope

# SCOPE

- Phạm vi (scope) trong JavaScript đề cập đến nơi mà biến và hàm có thể được truy cập và sử dụng. JavaScript sử dụng phạm vi để xác định "phạm vi" của một biến, tức là nơi mà biến đó có thể được sử dụng một cách hợp lệ trong chương trình.
<br>
- JavaScript có hai loại phạm vi cơ bản:
    - Phạm vi toàn cục (Global Scope):
    - Phạm vi cục bộ (Local Scope)
    - Phạm vi cục bộ và toàn cục lồng nhau:
        - Nắm vững khái niệm phạm vi (scope) là quan trọng để tránh các lỗi xung đột biến và 
        hiểu rõ cách các biến được quản lý trong chương trình JavaScript của bạn.

--------------------------------------------------------------------------------------------------------------

### Phạm vi toàn cục (Global Scope)

- Biến hoặc hàm được khai báo ở ngoài bất kỳ hàm hoặc khối mã nào được coi là có phạm vi toàn 
    cục và có thể truy cập từ bất kỳ đâu trong chương trình. 

- Biến toàn cục thường được định nghĩa bên ngoài tất cả các hàm và khối mã.

```javascript
    var globalVar = 10;

    function example() {
        console.log(globalVar); // Có thể truy cập biến globalVar ở đây
    }
```

--------------------------------------------------------------------------------------------------------------

### Phạm vi cục bộ (Local Scope):

- Biến hoặc hàm được khai báo trong một hàm hoặc khối mã cụ thể chỉ có thể truy cập bên 
    trong hàm hoặc khối mã đó. 
    Biến này không thể truy cập được từ bên ngoài hàm hoặc khối mã.

```javascript
    function example() {
        var localVar = 5; // Biến localVar có phạm vi cục bộ trong hàm example
        console.log(localVar); // Có thể truy cập localVar ở đây
    }

    console.log(localVar); // Không thể truy cập localVar ở đây, sẽ gây lỗi
```

--------------------------------------------------------------------------------------------------------------

### Phạm vi cục bộ và toàn cục lồng nhau (Code block):
    
- JavaScript cho phép bạn lồng các phạm vi cục bộ trong các phạm vi cục bộ khác. 

- Trong trường hợp này, biến ở phạm vi cục bộ ngoài có thể truy cập bên trong 
    phạm vi cục bộ nằm bên trong.

```javascript
    function outer() {
        var outerVar = 10;

        function inner() {
            var innerVar = 5;
            console.log(outerVar); // Có thể truy cập outerVar ở đây
        }

        inner();
    }

    outer();
```

---

## var, let, const trong scope

# [ Var ]

## Phạm vi
- var được sử dụng để khai báo biến với phạm vi toàn cục hoặc phạm vi hàm (function scope), tùy thuộc vào nơi biến được khai báo.
- Nếu var được khai báo bên trong một hàm, biến đó chỉ có thể truy cập được bên trong hàm đó.
    Nếu var được khai báo bên ngoài các hàm hoặc khối mã, biến đó có phạm vi toàn cục và có thể truy cập từ bất kỳ đâu trong chương trình. 

## Vòng đời
- Biến var tồn tại cho đến khi hàm hoặc chương trình kết thúc hoặc khi trình duyệt tắt.
- Biến var được "hoisting" lên đầu phạm vi, nghĩa là có thể được truy cập trước cả khi nó được khai báo trong mã, nhưng giá trị của nó sẽ là undefined cho đến khi nó được gán giá trị.

```javascript
    function example() {
        var x = 10; // Phạm vi hàm (function scope)
        if (true) {
            var y = 20; // Phạm vi hàm (function scope)
        }
    }
    console.log(x); // Lỗi, x không được truy cập ở ngoài hàm example
    console.log(y); // 20, y có thể truy cập ở ngoài khối if
```
<br>
<hr>

# [ Let ]

## Phạm vi
- Biến khai báo bằng let có phạm vi là phạm vi khối mã (block scope), nghĩa là biến chỉ có thể truy cập được bên trong khối mã mà nó được khai báo, bao gồm cả các khối if, for, while, và các hàm.

- Biến let không có phạm vi toàn cục.

## Vòng đời
- Biến let tồn tại từ lúc khai báo đến khi kết thúc khối mã mà nó nằm trong.
- Biến let không bị "hoisting" như biến var, nên nó chỉ có thể truy cập sau khi nó được khai báo.

```javascript
    function example() {
        let x = 10; // Phạm vi khối mã (block scope)
        if (true) {
            let y = 20; // Phạm vi khối mã (block scope)
        }
    }

    console.log(x); // Lỗi, x không được truy cập ở ngoài hàm example
    console.log(y); // Lỗi, y không được truy cập ở ngoài khối if
```

---------------------------------------------------------------------------------------------------------------

# [ Const ]

## Phạm vi
- Biến khai báo bằng const cũng có phạm vi là phạm vi khối mã (block scope), tương tự như let.
    Biến const không có phạm vi toàn cục.

## Vòng đời
- Biến const tồn tại từ lúc khai báo đến khi kết thúc khối mã mà nó nằm trong, giống như biến let.

- Tuy nhiên, biến const phải được gán giá trị ban đầu và không thể gán lại giá trị sau đó. 
- Điều này làm cho biến const trở thành một hằng số và không thể thay đổi giá trị của nó sau khi được khai báo.

```javascript
    function exampleConst() {
        const z = 30; // Biến z có phạm vi khối mã (block scope)

        if (true) {
            console.log(z); // 30, biến z có thể truy cập trong khối mã
        }

        console.log(z); // 30, biến z có thể truy cập từ bất kỳ đâu trong hàm
    }

    exampleConst();

    console.log(z); // Lỗi, biến z không có phạm vi toàn cục
```
