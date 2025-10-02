---
title: "Java NIO: Xử lý kết nối mạng Non-Blocking"
date: 2025-09-14
draft: false
tags: ["Java", "Networking", "NIO"]
---

## Giới thiệu

Trong các ứng dụng mạng truyền thống viết bằng **Java I/O**, mỗi kết nối từ client sẽ được gắn với một **thread riêng**. Cách tiếp cận này đơn giản nhưng có nhược điểm: khi số lượng kết nối tăng lên hàng nghìn, ứng dụng sẽ tiêu tốn rất nhiều bộ nhớ và CPU để quản lý thread.

Để giải quyết vấn đề này, từ **Java 1.4**, gói **Java NIO (New I/O)** được giới thiệu. NIO cho phép xử lý **non-blocking I/O**, nghĩa là một thread có thể quản lý nhiều kết nối cùng lúc, giúp ứng dụng **mở rộng (scalable)** hơn.

Trong bài viết này, chúng ta sẽ tìm hiểu:

1. Nguyên lý hoạt động của Java NIO.  
2. Các thành phần chính: Buffer, Channel, Selector.  
3. Ví dụ xây dựng server non-blocking bằng Java.  
4. So sánh I/O truyền thống và NIO.  
5. Ứng dụng thực tế.  

---

## Nguyên lý hoạt động của Java NIO

Trong mô hình NIO:

- **Channel** thay thế cho Stream: dữ liệu có thể đọc/ghi hai chiều.  
- **Buffer** là nơi lưu dữ liệu đọc/ghi.  
- **Selector** giúp một thread theo dõi nhiều channel và xử lý khi có sự kiện (như kết nối mới, dữ liệu đến).  

Thay vì blocking trên từng kết nối, thread chính chỉ **nghe sự kiện** và xử lý khi cần → tiết kiệm tài nguyên.

---

## Các thành phần chính của Java NIO

| Thành phần  | Mô tả                                                                 | Ví dụ class trong Java              |
|-------------|----------------------------------------------------------------------|-------------------------------------|
| **Buffer**  | Vùng nhớ tạm để đọc/ghi dữ liệu                                       | ByteBuffer, CharBuffer              |
| **Channel** | Đại diện cho kết nối tới socket hoặc file, đọc/ghi dữ liệu vào Buffer | SocketChannel, ServerSocketChannel  |
| **Selector**| Cho phép một thread giám sát nhiều channel, xử lý khi có sự kiện      | Selector                            |

---

## Ví dụ code

**Server non-blocking với Java NIO**

```java
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Iterator;

public class NIOServer {
    public static void main(String[] args) throws Exception {
        Selector selector = Selector.open();
        ServerSocketChannel server = ServerSocketChannel.open();
        server.bind(new InetSocketAddress(9000));
        server.configureBlocking(false);
        server.register(selector, SelectionKey.OP_ACCEPT);

        System.out.println("Server NIO đang chạy...");

        while (true) {
            selector.select();
            Iterator<SelectionKey> it = selector.selectedKeys().iterator();
            while (it.hasNext()) {
                SelectionKey key = it.next();
                it.remove();

                if (key.isAcceptable()) {
                    SocketChannel client = server.accept();
                    client.configureBlocking(false);
                    client.register(selector, SelectionKey.OP_READ);
                    System.out.println("Client kết nối: " + client.getRemoteAddress());
                } else if (key.isReadable()) {
                    SocketChannel client = (SocketChannel) key.channel();
                    ByteBuffer buffer = ByteBuffer.allocate(256);
                    int read = client.read(buffer);
                    if (read == -1) {
                        client.close();
                    } else {
                        String msg = new String(buffer.array()).trim();
                        System.out.println("Nhận: " + msg);
                    }
                }
            }
        }
    }
}
```

### So sánh Java I/O và Java NIO
| Tiêu chí                        | Java I/O (Blocking)                  | Java NIO (Non-Blocking)                              |
|---------------------------------|--------------------------------------|------------------------------------------------------|
| Mỗi kết nối cần thread riêng    | Có                                   | Không (1 thread có thể xử lý nhiều kết nối)          |
| Hiệu suất với nhiều client      | Thấp                                 | Cao                                                  |
| Độ phức tạp code                | Đơn giản                             | Phức tạp hơn                                         |
| Ứng dụng phù hợp                | Chương trình nhỏ, ít client          | Server lớn, chat, game online, web service           |


### Ứng dụng thực tế

- Java NIO là nền tảng cho nhiều framework mạnh mẽ:

- Netty: framework lập trình mạng hiệu suất cao, dùng trong nhiều hệ thống lớn.

- Vert.x: framework reactive hỗ trợ xử lý đồng thời hàng nghìn kết nối.

- Spring WebFlux: reactive web framework dựa trên Reactor và NIO.

### Kết luận

Java NIO giúp ứng dụng mạng trong Java mở rộng quy mô và tiết kiệm tài nguyên nhờ mô hình non-blocking.
Nếu bạn cần xây dựng server phục vụ hàng nghìn client đồng thời, NIO hoặc các framework dựa trên NIO là lựa chọn phù hợp.

Ngược lại, với các ứng dụng nhỏ, ít client, Java I/O truyền thống vẫn dễ triển khai và đủ mạnh.

🚀 Nắm vững NIO không chỉ giúp bạn viết ứng dụng mạng tốt hơn mà còn mở đường để học các framework hiện đại như Netty hay Spring WebFlux.