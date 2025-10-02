---
title: "TCP vs UDP trong Java: Nên chọn giao thức nào?"
date: 2025-09-14
draft: false
tags: ["Java", "Networking", "TCP", "UDP"]
---

## Giới thiệu

Trong lập trình mạng, một trong những câu hỏi cơ bản nhưng rất quan trọng là: **nên dùng TCP hay UDP để truyền dữ liệu?**  

Nếu bạn đang xây dựng một ứng dụng chat, một dịch vụ web, hay một trò chơi trực tuyến, việc lựa chọn đúng giao thức có thể ảnh hưởng lớn đến trải nghiệm người dùng. TCP và UDP đều nằm ở **lớp vận chuyển (Transport Layer)** của mô hình OSI, nhưng bản chất hoạt động của chúng hoàn toàn khác nhau.

Trong bài viết này, chúng ta sẽ cùng tìm hiểu:

1. TCP là gì, ưu và nhược điểm.  
2. UDP là gì, ưu và nhược điểm.  
3. Ví dụ cài đặt bằng **Java Socket API**.  
4. So sánh TCP và UDP trong thực tế.  
5. Khi nào nên chọn TCP, khi nào nên chọn UDP.  

---

## TCP là gì?

**TCP (Transmission Control Protocol)** là một giao thức hướng kết nối (connection-oriented). Trước khi truyền dữ liệu, TCP sẽ thực hiện **quy trình bắt tay 3 bước (three-way handshake)** để đảm bảo rằng cả hai bên đều sẵn sàng.

### Đặc điểm của TCP:
- **Đảm bảo dữ liệu toàn vẹn:** gói tin sẽ được sắp xếp đúng thứ tự.  
- **Đảm bảo không mất gói:** nếu gói tin bị mất, TCP sẽ gửi lại.  
- **Điều khiển luồng (Flow Control):** giúp tránh tình trạng tràn dữ liệu.  
- **Chậm hơn UDP**, vì cần nhiều bước kiểm tra.  

**Ví dụ Java: TCP Client**
```java
import java.io.OutputStream;
import java.net.Socket;

public class TCPClient {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("localhost", 8080);
            OutputStream out = socket.getOutputStream();
            out.write("Xin chào server qua TCP".getBytes());
            socket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**Ví dụ Java: TCP Server**
```java
Sao chép mã
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class TCPServer {
    public static void main(String[] args) {
        try {
            ServerSocket server = new ServerSocket(8080);
            System.out.println("TCP Server đang chạy...");
            Socket socket = server.accept();
            InputStream in = socket.getInputStream();
            byte[] buffer = new byte[1024];
            int read = in.read(buffer);
            System.out.println("Nhận từ client: " + new String(buffer, 0, read));
            server.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### UDP là gì?
UDP (User Datagram Protocol) là một giao thức không hướng kết nối (connectionless). Thay vì bắt tay, UDP gửi thẳng gói tin đi mà không cần biết bên kia có sẵn sàng hay không.

### Đặc điểm của UDP:
- Nhanh hơn TCP, vì không cần xác nhận.

- Không đảm bảo toàn vẹn dữ liệu: gói có thể mất, trùng, hoặc đảo thứ tự.

- Không có cơ chế kiểm soát luồng.

- Thường dùng cho các ứng dụng thời gian thực như video call, streaming, game.

**Ví dụ Java: UDP Client**
```java
Sao chép mã
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;

public class UDPClient {
    public static void main(String[] args) {
        try {
            DatagramSocket ds = new DatagramSocket();
            String msg = "Xin chào server qua UDP";
            byte[] buf = msg.getBytes();
            InetAddress ip = InetAddress.getByName("localhost");
            DatagramPacket dp = new DatagramPacket(buf, buf.length, ip, 8080);
            ds.send(dp);
            ds.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**Ví dụ Java: UDP Server**
```java
Sao chép mã
import java.net.DatagramPacket;
import java.net.DatagramSocket;

public class UDPServer {
    public static void main(String[] args) {
        try {
            DatagramSocket ds = new DatagramSocket(8080);
            System.out.println("UDP Server đang chạy...");
            byte[] buf = new byte[1024];
            DatagramPacket dp = new DatagramPacket(buf, buf.length);
            ds.receive(dp);
            System.out.println("Nhận từ client: " + new String(dp.getData(), 0, dp.getLength()));
            ds.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### So sánh TCP và UDP
| Tiêu chí            | TCP                                | UDP                          |
|----------------------|------------------------------------|------------------------------|
| Kiểu giao thức       | Connection-oriented                | Connectionless               |
| Đảm bảo dữ liệu      | Có                                 | Không                        |
| Thứ tự gói tin       | Đúng thứ tự                        | Có thể đảo lộn               |
| Hiệu năng            | Chậm hơn (nhiều kiểm tra)          | Nhanh hơn                    |
| Tài nguyên           | Tốn nhiều tài nguyên hơn           | Nhẹ hơn                      |
| Ứng dụng phổ biến    | Web, Email, FTP, Chat, SSH         | Game online, VoIP, Streaming |

**Khi nào chọn TCP, khi nào chọn UDP?**

- Dùng TCP khi ứng dụng yêu cầu dữ liệu chính xác, không mất mát.  
Ví dụ: web server, gửi mail, truyền file, đăng nhập hệ thống.

- Dùng UDP khi ưu tiên tốc độ và có thể chấp nhận mất gói.  
Ví dụ: game bắn súng online, video call, truyền hình trực tuyến.

### Kết luận
Cả TCP và UDP đều quan trọng trong lập trình mạng. TCP là lựa chọn hàng đầu khi cần độ tin cậy, còn UDP thích hợp cho các ứng dụng thời gian thực nơi tốc độ là ưu tiên số một.

Trong Java, cả hai đều có thư viện hỗ trợ sẵn (Socket/ServerSocket cho TCP, DatagramSocket/DatagramPacket cho UDP), giúp bạn dễ dàng triển khai tùy theo nhu cầu dự án.

Hy vọng sau bài viết này, bạn sẽ có cái nhìn rõ ràng hơn để quyết định khi nào dùng TCP, khi nào dùng UDP cho ứng dụng của mình. 🚀