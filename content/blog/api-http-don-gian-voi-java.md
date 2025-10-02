---
title: "Tự xây dựng API HTTP siêu nhẹ với Java"
date: 2025-09-14
draft: false
tags: ["Java", "Networking", "HTTP", "API"]
---

## Giới thiệu

Trong thế giới lập trình hiện đại, **API HTTP** đóng vai trò trung tâm: từ ứng dụng web, mobile cho đến hệ thống phân tán, mọi thứ đều giao tiếp thông qua HTTP. Thông thường, lập trình viên sẽ sử dụng các framework mạnh mẽ như **Spring Boot**, **Quarkus** hay **Micronaut** để xây dựng API.

Tuy nhiên, đôi khi bạn chỉ cần một API nhỏ, nhanh gọn, để phục vụ demo, học tập hoặc viết thử nghiệm. Lúc này, sử dụng các framework lớn có thể là “quá sức”. Thay vào đó, chúng ta có thể tự viết một API siêu nhẹ bằng **Java thuần** hoặc dùng thư viện tối giản như **SparkJava**.

Trong bài viết này, bạn sẽ học cách:

1. Hiểu cơ chế API HTTP cơ bản.  
2. Viết API HTTP chỉ với **Java thuần** (không framework).  
3. Viết API HTTP với **SparkJava**.  
4. So sánh ưu – nhược điểm.  
5. Ứng dụng thực tế.  

## API HTTP là gì?

**API (Application Programming Interface)** cho phép ứng dụng này giao tiếp với ứng dụng khác.  
**HTTP (HyperText Transfer Protocol)** là giao thức phổ biến để xây dựng API.  

Một API HTTP đơn giản thường có các **endpoint** như sau:

| Phương thức | Đường dẫn         | Chức năng                  |
|-------------|-------------------|----------------------------|
| GET         | `/hello`          | Lấy dữ liệu chào mừng       |
| POST        | `/users`          | Tạo người dùng mới          |
| GET         | `/users/{id}`     | Lấy thông tin người dùng    |
| PUT         | `/users/{id}`     | Cập nhật thông tin người dùng |
| DELETE      | `/users/{id}`     | Xóa người dùng              |

---

## Viết API HTTP bằng Java thuần

Java cung cấp sẵn `com.sun.net.httpserver.HttpServer` để tạo web server đơn giản.  
**Ví dụ API `/hello`:**

```java
import com.sun.net.httpserver.HttpServer;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.HttpExchange;
import java.io.OutputStream;
import java.net.InetSocketAddress;

public class SimpleHttpServer {
    public static void main(String[] args) throws Exception {
        HttpServer server = HttpServer.create(new InetSocketAddress(8080), 0);
        server.createContext("/hello", new HelloHandler());
        server.setExecutor(null); // dùng executor mặc định
        server.start();
        System.out.println("Server chạy tại http://localhost:8080/hello");
    }

    static class HelloHandler implements HttpHandler {
        @Override
        public void handle(HttpExchange exchange) {
            try {
                String response = "Xin chào từ Java HTTP Server!";
                exchange.sendResponseHeaders(200, response.getBytes().length);
                OutputStream os = exchange.getResponseBody();
                os.write(response.getBytes());
                os.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```
👉 Chạy chương trình, mở trình duyệt tại http://localhost:8080/hello, bạn sẽ thấy dòng chữ:
“Xin chào từ Java HTTP Server!”

### So sánh các cách tiếp cận
| Tiêu chí           | Java thuần (HttpServer) | SparkJava                       |
|--------------------|--------------------------|---------------------------------|
| Độ phức tạp code   | Nhiều boilerplate        | Ngắn gọn, dễ đọc                |
| Phụ thuộc          | Không cần thư viện ngoài | Cần thêm SparkJava              |
| Hiệu suất          | Tốt cho API nhỏ          | Tốt, tối ưu cho microservice    |
| Tính năng mở rộng  | Giới hạn (cần viết thêm) | Có middleware, template, JSON parser |

### Ứng dụng thực tế

- Java thuần: phù hợp khi học tập, viết demo nhanh, hoặc khi bạn muốn kiểm soát chi tiết mọi thứ.

- SparkJava: phù hợp cho microservice, ứng dụng nhỏ cần triển khai nhanh mà không muốn Spring Boot quá nặng nề.

### Kết luận

Việc tự viết API HTTP siêu nhẹ giúp bạn hiểu rõ hơn về cách một web server hoạt động từ bên trong.

Nếu muốn tối giản tuyệt đối, hãy dùng HttpServer trong Java thuần.

Nếu muốn gọn nhẹ nhưng vẫn tiện ích, hãy chọn SparkJava.

🚀 Bước tiếp theo, bạn có thể thử thêm các endpoint mới, tích hợp với cơ sở dữ liệu, hoặc viết một ứng dụng CRUD hoàn chỉnh dựa trên API HTTP mini này.