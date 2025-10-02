---
title: "WebSocket với Node.js: Giao tiếp real-time đơn giản"
date: 2025-09-14
draft: false
tags: ["JavaScript", "Node.js", "WebSocket", "Realtime"]
---

## Giới thiệu

Trong các ứng dụng hiện đại như **chat online**, **game multiplayer**, hay **ứng dụng theo dõi chứng khoán**, yêu cầu quan trọng là **dữ liệu phải được cập nhật theo thời gian thực (real-time)**.  
HTTP truyền thống hoạt động theo cơ chế **request – response**: client gửi yêu cầu, server trả về dữ liệu. Điều này không phù hợp cho các ứng dụng real-time, nơi dữ liệu liên tục thay đổi.

**WebSocket** ra đời để giải quyết vấn đề này. Nó cung cấp một kênh **kết nối 2 chiều (full-duplex)** giữa client và server, cho phép **gửi và nhận dữ liệu liên tục** mà không cần mỗi lần đều phải request.

Trong bài viết này, chúng ta sẽ:

1. Tìm hiểu nguyên lý hoạt động của WebSocket.  
2. Viết server WebSocket với Node.js.  
3. Viết client WebSocket bằng HTML/JavaScript.  
4. So sánh WebSocket với HTTP truyền thống.  
5. Ứng dụng thực tế.  

---

## WebSocket hoạt động như thế nào?

- Kết nối bắt đầu bằng **handshake qua HTTP** (client yêu cầu nâng cấp lên WebSocket).  
- Sau khi handshake thành công, cả client và server giữ kết nối mở.  
- Dữ liệu có thể **truyền 2 chiều** liên tục, gần như không độ trễ.  

👉 Đây là điểm khác biệt quan trọng với HTTP truyền thống: **client không cần gửi request mới để nhận dữ liệu**.

---

## Ví dụ: Tạo WebSocket server với Node.js

Chúng ta sẽ dùng thư viện **ws** – gọn nhẹ, phổ biến trong Node.js.

**Cài đặt:**
```bash
npm install ws
```
Tạo server WebSocket:

```javascript
// file: server.js
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', ws => {
  console.log('Client đã kết nối');

  ws.on('message', message => {
    console.log(`Nhận từ client: ${message}`);
    ws.send(`Server nhận được: ${message}`);
  });

  ws.send('Xin chào từ WebSocket Server!');
});

console.log("WebSocket server chạy tại ws://localhost:8080");
```
Ví dụ: Client WebSocket
Trong file HTML, ta có thể viết:

```html
<!DOCTYPE html>
<html>
<head>
  <title>WebSocket Demo</title>
</head>
<body>
  <h2>Kết nối WebSocket</h2>
  <input id="msg" type="text" placeholder="Nhập tin nhắn..." />
  <button onclick="sendMessage()">Gửi</button>
  <ul id="chat"></ul>

  <script>
    const socket = new WebSocket("ws://localhost:8080");

    socket.onopen = () => {
      console.log("Đã kết nối tới server");
    };

    socket.onmessage = (event) => {
      const li = document.createElement("li");
      li.innerText = "Server: " + event.data;
      document.getElementById("chat").appendChild(li);
    };

    function sendMessage() {
      const input = document.getElementById("msg");
      socket.send(input.value);
      input.value = "";
    }
  </script>
</body>
</html>
```
👉 Khi mở trang HTML và nhập tin nhắn, client sẽ gửi dữ liệu qua WebSocket đến server và nhận phản hồi ngay lập tức.

### So sánh WebSocket và HTTP truyền thống
| Tiêu chí              | HTTP (Request – Response)         | WebSocket (Full-duplex)         |
|------------------------|-----------------------------------|---------------------------------|
| Kết nối                | Mỗi request mở & đóng             | Giữ kết nối liên tục            |
| Chiều giao tiếp        | 1 chiều (client → server)         | 2 chiều (client ↔ server)       |
| Độ trễ                 | Cao (mỗi lần phải request)        | Thấp (truyền ngay lập tức)      |
| Hiệu quả real-time     | Không phù hợp                     | Rất phù hợp                     |
| Ứng dụng               | Web truyền thống, REST API        | Chat, game online, IoT, dashboard |


### Ứng dụng thực tế
- Ứng dụng chat: Messenger, Slack, Zalo.

- Game multiplayer online: đồng bộ trạng thái người chơi.

- Realtime dashboard: hiển thị log hệ thống, chứng khoán, giá crypto.

- IoT: kết nối thiết bị thông minh, gửi dữ liệu sensor liên tục.

### Kết luận
WebSocket mở ra khả năng truyền dữ liệu 2 chiều, real-time giữa client và server, giúp các ứng dụng hiện đại mượt mà và phản hồi nhanh hơn.
Khi kết hợp với Node.js, việc xây dựng một WebSocket server cực kỳ đơn giản, chỉ vài dòng code.

🚀 Nếu bạn đang làm ứng dụng cần real-time, hãy thử WebSocket – nó sẽ thay đổi hoàn toàn cách bạn xây dựng hệ thống.

