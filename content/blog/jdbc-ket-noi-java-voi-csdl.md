---
title: "JDBC cơ bản: Kết nối Java với cơ sở dữ liệu"
date: 2025-09-14
draft: false
tags: ["Java", "Database", "JDBC", "SQL"]
---

## Giới thiệu

Trong lập trình ứng dụng, một nhu cầu rất phổ biến là **làm việc với cơ sở dữ liệu (CSDL)**. Dữ liệu người dùng, sản phẩm, đơn hàng… đều cần được lưu trữ và truy xuất hiệu quả.  

Trong Java, để kết nối và làm việc với CSDL, ta dùng **JDBC (Java Database Connectivity)** – một API chuẩn, có trong JDK từ những phiên bản đầu tiên. JDBC cho phép Java giao tiếp với hầu hết các hệ quản trị CSDL (MySQL, PostgreSQL, Oracle, SQL Server…) thông qua **Driver** tương ứng.  

Trong bài viết này, chúng ta sẽ cùng tìm hiểu:

1. JDBC là gì và hoạt động như thế nào.  
2. Các bước cơ bản khi kết nối Java với CSDL.  
3. Ví dụ kết nối với MySQL.  
4. So sánh `Statement`, `PreparedStatement`, `CallableStatement`.  
5. Ứng dụng thực tế và best practice.  

---

## JDBC hoạt động như thế nào?

JDBC hoạt động theo cơ chế trung gian:  
- Ứng dụng Java gọi các hàm JDBC (API chuẩn).  
- JDBC gọi **Driver** (do nhà cung cấp CSDL phát triển).  
- Driver thực hiện truy vấn SQL tới CSDL và trả về kết quả cho Java.  

Có thể hình dung như sau:

```Java 
App -> JDBC API -> JDBC Driver -> Database
```
---

## Các bước cơ bản để kết nối CSDL bằng JDBC

1. **Load Driver** (từ JDBC 4.0 trở đi, chỉ cần có thư viện trong classpath, không cần gọi thủ công).  
2. **Tạo kết nối** bằng `DriverManager.getConnection()`.  
3. **Tạo Statement** để thực thi SQL.  
4. **Thực thi truy vấn** (`executeQuery` hoặc `executeUpdate`).  
5. **Xử lý kết quả** từ `ResultSet`.  
6. **Đóng kết nối** để giải phóng tài nguyên.  

---

## Ví dụ: Kết nối MySQL bằng JDBC

**Tạo bảng users trong MySQL**

```sql
CREATE DATABASE testdb;
USE testdb;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
```

**Java code: Kết nối và thêm dữ liệu**

```java
Sao chép mã
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class JDBCDemo {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/testdb";
        String user = "root";
        String password = "1234";

        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            System.out.println("Kết nối thành công!");

            // Thêm dữ liệu
            String insert = "INSERT INTO users (name, email) VALUES (?, ?)";
            PreparedStatement ps = conn.prepareStatement(insert);
            ps.setString(1, "Nguyen Van A");
            ps.setString(2, "a@example.com");
            ps.executeUpdate();

            // Truy vấn dữ liệu
            ResultSet rs = conn.createStatement().executeQuery("SELECT * FROM users");
            while (rs.next()) {
                System.out.println(rs.getInt("id") + " - " 
                    + rs.getString("name") + " - " + rs.getString("email"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
👉 Chạy chương trình, bạn sẽ thấy dữ liệu được in ra từ CSDL MySQL.

### So sánh Statement, PreparedStatement, CallableStatement
| Loại              | Mục đích                               | Ưu điểm                                           | Nhược điểm                  |
|--------------------|----------------------------------------|--------------------------------------------------|-----------------------------|
| Statement          | Thực thi câu SQL tĩnh (không có tham số) | Đơn giản, dễ dùng                                | Dễ bị lỗi SQL Injection     |
| PreparedStatement  | Câu SQL có tham số (dùng ?)             | Bảo mật hơn, tránh SQL Injection, tái sử dụng hiệu quả | Phải viết thêm placeholder ? |
| CallableStatement  | Gọi Stored Procedure trong DB           | Hỗ trợ thủ tục lưu trữ phức tạp                   | Khó debug hơn               |


### Best Practices khi dùng JDBC
Luôn đóng Connection, Statement, ResultSet sau khi dùng.

Nên dùng try-with-resources (Java 7+) để tự động đóng tài nguyên.

Dùng PreparedStatement thay vì Statement để tránh SQL Injection.

Tách code JDBC ra thành DAO (Data Access Object) để dễ bảo trì.

Nếu ứng dụng lớn → cân nhắc dùng ORM framework như Hibernate, JPA, MyBatis.

### Ứng dụng thực tế
- Quản lý thông tin người dùng trong website.

- Lưu trữ log hệ thống vào database.

- Xây dựng backend CRUD API với dữ liệu trong MySQL/PostgreSQL.

### Kết luận
JDBC là kiến thức cơ bản nhưng cực kỳ quan trọng cho lập trình viên Java.
Nó giúp bạn hiểu rõ cách Java làm việc với cơ sở dữ liệu, từ đó có thể tự tin hơn khi học các framework cao hơn như Hibernate hay Spring Data JPA.

🚀 Nếu bạn nắm vững JDBC, bạn sẽ dễ dàng xây dựng các ứng dụng quản lý dữ liệu và backend API bằng Java.