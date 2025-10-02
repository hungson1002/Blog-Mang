---
title: "Kiểm thử ứng dụng mạng bằng JUnit & Testcontainers"
date: 2025-09-14
draft: false
tags: ["Java", "Networking", "JUnit", "Testcontainers", "Testing"]
---

## Giới thiệu

Trong phát triển phần mềm, viết code chỉ là bước đầu tiên. Quan trọng hơn là **kiểm thử (testing)** để đảm bảo hệ thống hoạt động đúng và ổn định.  

Với các ứng dụng mạng (sử dụng **Socket, HTTP API, Database**), việc kiểm thử phức tạp hơn nhiều so với code thuần túy, vì cần môi trường thực tế: server, database, hoặc thậm chí nhiều service chạy song song.  

👉 Đây là lúc **JUnit** và **Testcontainers** phát huy sức mạnh:  
- **JUnit**: framework kiểm thử phổ biến nhất trong Java.  
- **Testcontainers**: thư viện cho phép chạy môi trường thực tế (như MySQL, Redis, Kafka, API mock…) bên trong **Docker container** ngay trong test.  

Trong bài viết này, ta sẽ tìm hiểu:  
1. Tại sao kiểm thử ứng dụng mạng khó khăn.  
2. Giải pháp với JUnit và Testcontainers.  
3. Ví dụ kiểm thử API HTTP bằng JUnit.  
4. Ví dụ kiểm thử Database với Testcontainers.  
5. So sánh cách kiểm thử truyền thống và Testcontainers.  
6. Kết luận và ứng dụng thực tế.  

---

## Khó khăn khi kiểm thử ứng dụng mạng

- **Phụ thuộc môi trường**: cần database, server hoặc service bên ngoài.  
- **Khó tái tạo lỗi**: mạng có độ trễ, mất gói, hoặc lỗi kết nối không ổn định.  
- **Cấu hình phức tạp**: phải cài đặt nhiều service (MySQL, Redis, Kafka...) trước khi chạy test.  

👉 Nếu chỉ chạy test bằng “mock object” thì không đảm bảo, vì mock không phản ánh môi trường thật.  

---

## Giải pháp: JUnit + Testcontainers

- **JUnit**: viết test case rõ ràng, chạy tự động.  
- **Testcontainers**: tạo **Docker container tạm thời** cho test (ví dụ MySQL, PostgreSQL, Redis, Kafka...).  

Điểm mạnh:  
- Test **gần với môi trường thật nhất**.  
- Tự động tạo & xóa container sau khi test xong.  
- Không cần cài database thủ công trên máy dev.  

---

## Ví dụ 1: Kiểm thử API HTTP với JUnit

Giả sử ta có API đơn giản viết bằng SparkJava:

```java
import static spark.Spark.*;

public class SimpleApi {
    public static void main(String[] args) {
        get("/hello", (req, res) -> "Hello World");
    }
}
```

**Viết test bằng JUnit 5 + HttpClient:**

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
import java.net.http.*;

public class ApiTest {
    @Test
    void testHelloApi() throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest req = HttpRequest.newBuilder()
                .uri(new java.net.URI("http://localhost:4567/hello"))
                .build();
        HttpResponse<String> res = client.send(req, HttpResponse.BodyHandlers.ofString());
        assertEquals(200, res.statusCode());
        assertEquals("Hello World", res.body());
    }
}
```
**Ví dụ 2: Kiểm thử Database với Testcontainers**
Thêm dependency (Maven):

```xml
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>mysql</artifactId>
  <version>1.19.0</version>
  <scope>test</scope>
</dependency>
```
Test code:

```java
import org.junit.jupiter.api.Test;
import org.testcontainers.containers.MySQLContainer;
import java.sql.*;

import static org.junit.jupiter.api.Assertions.*;

public class DatabaseTest {
    @Test
    void testInsertUser() throws Exception {
        try (MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0")) {
            mysql.start();

            Connection conn = DriverManager.getConnection(
                    mysql.getJdbcUrl(), mysql.getUsername(), mysql.getPassword());

            Statement stmt = conn.createStatement();
            stmt.execute("CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(100))");
            stmt.execute("INSERT INTO users (name) VALUES ('Alice')");

            ResultSet rs = stmt.executeQuery("SELECT COUNT(*) FROM users");
            rs.next();
            int count = rs.getInt(1);

            assertEquals(1, count);

            mysql.stop();
        }
    }
}
```
👉 Ở đây, MySQL sẽ được chạy trong container Docker, sau khi test xong nó sẽ tự động bị xóa.

### So sánh cách kiểm thử
| Tiêu chí              | Kiểm thử truyền thống (Mock/Manual DB) | JUnit + Testcontainers              |
|------------------------|----------------------------------------|-------------------------------------|
| Mức độ thực tế         | Thấp (chỉ giả lập)                     | Cao (dùng container thật)           |
| Độ tin cậy             | Thấp                                   | Cao                                 |
| Cấu hình môi trường    | Khó, tốn công                          | Tự động, chạy container             |
| Tái tạo lỗi            | Khó                                   | Dễ (container luôn reset như mới)   |
| Tích hợp CI/CD         | Phức tạp                               | Dễ dàng                             |


### Ứng dụng thực tế
- Viết test cho ứng dụng Spring Boot kết nối MySQL/PostgreSQL.

- Test Kafka/Redis/Elasticsearch mà không cần cài đặt thật.

- Dùng trong CI/CD pipeline để đảm bảo mỗi lần build đều chạy test trên môi trường sạch.

### Kết luận
Kết hợp JUnit và Testcontainers giúp kiểm thử ứng dụng mạng dễ dàng hơn nhiều:
- Không còn lo lắng về cài đặt môi trường thủ công.

- Test chính xác hơn, gần với môi trường production.

- Thích hợp để tích hợp vào pipeline CI/CD.

🚀 Với kiến thức này, bạn đã có thể tự tin viết test không chỉ cho logic ứng dụng, mà còn cho toàn bộ hệ thống mạng và database của mình.