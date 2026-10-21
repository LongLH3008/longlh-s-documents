# REST API: JSON Server, Postman, Fetch

## JSON Server

```text
link git json-server
https://github.com/typicode/json-server


#1 Install JSON Server on terminal

    npm install -g json-server


#2 Create a db.json file with some data

    {
    "posts": [
        { "id": 1, "title": "json-server", "author": "typicode" }
    ],
    "comments": [
        { "id": 1, "body": "some comment", "postId": 1 }
    ],
    "profile": { "name": "typicode" }
    }

#3 Add into script in package.json
    "start" : "json-server --watch db.json"

#4 Start JSON Server

    npm start

    Now if you go to http://localhost:3000/posts/1, you'll get

    { "id": 1, "title": "json-server", "author": "typicode" }
```

---

## Postman & REST API

- JSON Server: API Server (Fake) / Mock API

Sử dụng post man làm việc với rest api
https://fullstack.edu.vn/learning/javascript-co-ban?id=61546753-d397-43ef-b948-ec7f3b3fd880

- CRUD:
    - Create: Tạo mới -> POST
    - Read: Đọc dữ liệu -> GET
    - Update: cập nhật, sửa -> PUT / PATCH
    - Delete: Xóa bỏ -> DELETE

---

## Fetch

Front-End:
  Xây dựng giao diện người dùng

Back-End:
  Xây dựng logic xử lí dữ liệu
  Cơ sở dữ liệu

API (URL) -> Application progamming interface
  - Cổng giao tiếp giữa các phần mềm

BackEnd -> API -> FETCH -> JSON / XML -> JSON.parse -> Javascript types -> Render giao diện với html
https://jsonplaceholder.typicode.com/

response.json();

```js
var postAPI = 'https://jsonplaceholder.typicode.com/posts'


fetch(postAPI)
    .then(function (response) {
        return response.json();
        // trả về 1 promise
        // JSON.parse: JSON -> Javascript types
    })
    .then (function (posts) {
        console.log(posts);
        // var htmls = posts.map( (post) => {
        //     return `
        //     <li id="${post.id}">
        //         <h2>${post.title}</h2>
        //         <p>${post.body}</p>
        //     </li>`;
        // })

        // var html = htmls.join('');
        // document.getElementById('post-block').innerHTML = html;
    })
    .catch (function (err) {
        console.log('Error: ', err);
    })

function checkAnswer() {
    
}
```
