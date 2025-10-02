---
title: "Hiểu rõ Fetch API & vấn đề CORS trong JavaScript"
date: 2025-09-14
draft: false
tags: ["JavaScript", "Fetch API", "CORS", "Web"]
---

## Giới thiệu

Trong lập trình web hiện đại, **JavaScript** đóng vai trò trung tâm trong việc giao tiếp giữa frontend và backend.  
Một trong những công cụ mạnh mẽ nhất để gọi API từ trình duyệt là **Fetch API**. Nó thay thế `XMLHttpRequest` cũ kỹ, mang đến cú pháp gọn gàng hơn dựa trên **Promise** và hỗ trợ tốt với **async/await**.  

Tuy nhiên, khi gọi API từ domain khác, bạn thường gặp lỗi khó chịu:  

``` Access to fetch at 'http://api.example.com'
from origin 'http://localhost:3000' has been blocked by CORS policy
```

👉 Đây chính là **CORS (Cross-Origin Resource Sharing)** – cơ chế bảo mật của trình duyệt.  

Trong bài viết này, ta sẽ tìm hiểu:  
1. Fetch API là gì và cú pháp cơ bản.  
2. Các ví dụ sử dụng Fetch API.  
3. CORS là gì và tại sao gây lỗi.  
4. Cách backend và frontend xử lý CORS.  
5. So sánh Fetch API với XMLHttpRequest.  
6. Kết luận và best practices.  

---

## Fetch API cơ bản

Ví dụ gọi API GET:

```javascript
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
Hoặc với async/await:

javascript
Sao chép mã
async function getPost() {
  try {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts/1");
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
getPost();
```

Ví dụ POST dữ liệu:

```javascript
Sao chép mã
fetch("https://jsonplaceholder.typicode.com/posts", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    title: "Hello",
    body: "Bài viết test",
    userId: 1
  })
})
  .then(res => res.json())
  .then(data => console.log(data));
```
### CORS là gì?
CORS (Cross-Origin Resource Sharing) là cơ chế bảo mật của trình duyệt:

Ngăn frontend từ domain A gọi API sang domain B nếu backend B không cho phép.

Được thực hiện bằng cách kiểm tra HTTP Header Access-Control-Allow-Origin.

Ví dụ:

```Frontend chạy ở http://localhost:3000.```

```Backend API ở http://api.example.com.```

Nếu API không trả header:

```arduino
Access-Control-Allow-Origin: http://localhost:3000
```
→ Trình duyệt sẽ chặn request và báo lỗi CORS.

Cách xử lý CORS
Backend thêm header CORS
Ví dụ với ExpressJS:

```javascript
const express = require("express");
const cors = require("cors");
const app = express();

app.use(cors()); // cho phép mọi origin
// app.use(cors({ origin: "http://localhost:3000" })); // chỉ cho phép 1 domain

app.get("/data", (req, res) => {
  res.json({ msg: "Hello từ server!" });
});

app.listen(4000, () => console.log("Server chạy ở cổng 4000"));
```
Frontend proxy request
Trong React (Create React App), có thể thêm vào package.json:

```
"proxy": "http://localhost:4000"
```
Khi đó gọi /data sẽ được chuyển tiếp sang backend, tránh lỗi CORS.

### So sánh Fetch API và XMLHttpRequest
| Tiêu chí             | Fetch API                               | XMLHttpRequest (XHR)                |
|-----------------------|-----------------------------------------|-------------------------------------|
| Cú pháp              | Ngắn gọn, dùng Promise/async-await       | Dài dòng, callback-based            |
| Hỗ trợ stream        | Có                                      | Không                               |
| Xử lý lỗi HTTP       | Không tự throw (phải check `res.ok`)    | Tự quản lý status code              |
| Được hỗ trợ rộng rãi | Tất cả browser hiện đại                  | Vẫn hỗ trợ nhưng dần lỗi thời       |


### Ứng dụng thực tế
- Gọi API REST từ frontend React/Vue/Angular.

- Upload file qua API.

- Tích hợp API bên thứ ba (Google, Facebook, GitHub).

- Xây dựng ứng dụng SPA cần giao tiếp với backend.

### Kết luận
Fetch API là cách hiện đại và gọn gàng để gọi HTTP request từ frontend.

CORS là lớp bảo mật bắt buộc của trình duyệt, không phải lỗi của code frontend.

Giải pháp: cấu hình backend đúng cách hoặc dùng proxy.

🚀 Khi hiểu rõ Fetch API và CORS, bạn sẽ tự tin hơn trong việc xây dựng các ứng dụng web hiện đại, tránh được những lỗi “khó hiểu” khi gọi API giữa các domain khác nhau