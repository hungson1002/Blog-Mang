---
title: "REST vs GraphQL: So sánh và cách lựa chọn"
date: 2025-09-14
draft: false
tags: ["API", "REST", "GraphQL", "Web Development"]
---

## Giới thiệu

Trong lập trình web và mobile, **API** là cầu nối giữa client và server. Trong nhiều năm, **REST (Representational State Transfer)** đã trở thành chuẩn mực. Tuy nhiên, khi ứng dụng ngày càng phức tạp, REST dần bộc lộ hạn chế.  

**GraphQL** ra đời (2015, bởi Facebook) như một giải pháp thay thế REST, cho phép client **yêu cầu chính xác dữ liệu cần thiết**. Ngày nay, nhiều công ty lớn như GitHub, Shopify, Twitter đều cung cấp API GraphQL.

Trong bài viết này, chúng ta sẽ:

1. Tìm hiểu REST API và GraphQL API.  
2. Xem ví dụ minh họa.  
3. So sánh ưu – nhược điểm.  
4. Đề xuất khi nào chọn REST, khi nào chọn GraphQL.  

---

## REST API là gì?

REST API dựa trên **HTTP và tài nguyên (resource)**.  
Mỗi tài nguyên (ví dụ `users`, `posts`) có một URL riêng và được thao tác bằng các phương thức HTTP:

- `GET /users` → Lấy danh sách người dùng  
- `GET /users/1` → Lấy thông tin user có ID = 1  
- `POST /users` → Tạo user mới  
- `PUT /users/1` → Cập nhật user ID = 1  
- `DELETE /users/1` → Xóa user  

Ví dụ trả về JSON:

```json
{
  "id": 1,
  "name": "Nguyen Van A",
  "email": "vana@example.com"
}
```
### GraphQL API là gì?
GraphQL chỉ có 1 endpoint duy nhất (thường là /graphql).
Client sẽ gửi query để lấy dữ liệu đúng như mong muốn.

Ví dụ query lấy user có id = 1 và chỉ cần tên + email:

```graphql
{
  user(id: 1) {
    name
    email
  }
}
```
👉 Kết quả trả về:

```json
{
  "data": {
    "user": {
      "name": "Nguyen Van A",
      "email": "vana@example.com"
    }
  }
}
```
So với REST (thường trả về nhiều trường không cần thiết), GraphQL giúp tối ưu dữ liệu truyền tải.

### So sánh REST và GraphQL
| Tiêu chí                  | REST API                                        | GraphQL API                                         |
|----------------------------|-------------------------------------------------|-----------------------------------------------------|
| Endpoint                   | Nhiều (mỗi tài nguyên 1 URL)                    | Một endpoint duy nhất `/graphql`                    |
| Dữ liệu trả về             | Cố định, có thể thừa dữ liệu                    | Linh hoạt, chỉ lấy đúng dữ liệu cần                 |
| Overfetching/Underfetching | Thường gặp (thừa/thiếu dữ liệu)                 | Không xảy ra                                        |
| Cách gọi API               | Theo tài nguyên và HTTP method                  | Query hoặc Mutation                                 |
| Học tập, triển khai        | Dễ, phổ biến rộng rãi                           | Phức tạp hơn, cần học query language                |
| Caching                    | Hỗ trợ tốt (HTTP cache)                         | Phức tạp hơn, cần giải pháp riêng                   |
| Khi phù hợp                | Ứng dụng nhỏ, API đơn giản, public API          | Ứng dụng lớn, client đa dạng (web, mobile)         |


Ví dụ REST vs GraphQL
REST – lấy user + danh sách bài viết:

Gọi `GET /users/1` → trả về thông tin user.

Gọi tiếp `GET /users/1/posts` → trả về danh sách bài viết.

👉 Phải gọi 2 API.

GraphQL – lấy user + bài viết trong 1 query:

```graphql
{
  user(id: 1) {
    name
    email
    posts {
      title
      createdAt
    }
  }
}
```
👉 Chỉ cần 1 request là đủ.

### Khi nào chọn REST, khi nào chọn GraphQL?
**Chọn REST nếu:**

- Ứng dụng nhỏ, API đơn giản.

- Muốn tận dụng cache HTTP có sẵn.

- Cần triển khai nhanh, dễ học, dễ tích hợp.

**Chọn GraphQL nếu:**

- Ứng dụng lớn, dữ liệu phức tạp, client đa dạng.

- Muốn giảm số lượng request.

- Muốn client chủ động chọn dữ liệu.

### Kết luận
REST và GraphQL đều là công cụ mạnh mẽ cho lập trình API.

REST: đơn giản, phổ biến, dễ áp dụng cho dự án nhỏ và public API.

GraphQL: hiện đại, tối ưu hóa dữ liệu, phù hợp cho ứng dụng phức tạp và đa nền tảng.

🚀 Không có giải pháp nào “tuyệt đối tốt hơn”, mà quan trọng là chọn đúng công cụ cho đúng bài toán.