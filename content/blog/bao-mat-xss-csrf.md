---
title: "Bảo mật Web: Ngăn chặn XSS & CSRF với JavaScript"
date: 2025-09-14
draft: false
tags: ["JavaScript", "Security", "XSS", "CSRF", "Web"]
---

## Giới thiệu

Trong thời đại web ngày nay, **bảo mật ứng dụng** là yếu tố sống còn. Hai trong số các lỗ hổng phổ biến nhất mà lập trình viên hay gặp phải là **XSS (Cross-Site Scripting)** và **CSRF (Cross-Site Request Forgery)**.  

Nếu không được ngăn chặn, kẻ tấn công có thể:

- Chèn mã độc vào trình duyệt người dùng (XSS).  
- Lợi dụng người dùng đã đăng nhập để thực hiện hành động trái phép (CSRF).  

Trong bài viết này, chúng ta sẽ:

1. Hiểu XSS là gì và cách khai thác.  
2. Hiểu CSRF là gì và cách khai thác.  
3. Viết ví dụ JavaScript minh họa.  
4. So sánh XSS và CSRF.  
5. Cách phòng chống trong ứng dụng web.  

---

## XSS (Cross-Site Scripting) là gì?

**XSS** xảy ra khi ứng dụng web cho phép người dùng nhập dữ liệu mà **không kiểm soát hoặc lọc đầu vào**, sau đó hiển thị trực tiếp dữ liệu đó lên trang.

Ví dụ lỗ hổng XSS:

```html
<input type="text" id="comment" />
<button onclick="postComment()">Gửi</button>
<div id="comments"></div>

<script>
function postComment() {
  const input = document.getElementById("comment").value;
  // ❌ Lỗi: chèn trực tiếp HTML mà không escape
  document.getElementById("comments").innerHTML += input;
}
</script>
```
Nếu người dùng nhập vào:

```html
<script>alert("Bị hack rồi!");</script>
```
👉 Script này sẽ được thực thi trong trình duyệt, gây nguy hiểm.

### CSRF (Cross-Site Request Forgery) là gì?
CSRF xảy ra khi kẻ tấn công lợi dụng phiên đăng nhập (session/cookie) của người dùng để gửi yêu cầu giả mạo.

Ví dụ: Một website có endpoint xóa tài khoản:

```
POST https://example.com/deleteAccount
```
Người dùng đã đăng nhập, cookie session lưu trong trình duyệt. Nếu kẻ xấu gửi link:

```html
<img src="https://example.com/deleteAccount" />
```
👉 Khi người dùng mở link, trình duyệt tự động gửi request kèm `cookie session` → tài khoản có thể bị xóa mà người dùng không biết.

### So sánh XSS và CSRF
| Tiêu chí              | XSS (Cross-Site Scripting)                  | CSRF (Cross-Site Request Forgery)             |
|-----------------------|---------------------------------------------|-----------------------------------------------|
| Cách khai thác         | Chèn mã JavaScript vào trang web            | Lợi dụng cookie/session của người dùng        |
| Hệ quả                 | Đánh cắp dữ liệu, chiếm quyền điều khiển UI | Thực hiện hành động giả mạo thay người dùng   |
| Phụ thuộc người dùng   | Người dùng xem nội dung chứa mã độc         | Người dùng đã đăng nhập vào ứng dụng          |
| Ngăn chặn              | Escape dữ liệu, Content Security Policy     | CSRF token, SameSite cookie                   |

**Cách phòng chống XSS Escape đầu vào/đầu ra:** không chèn trực tiếp dữ liệu người dùng vào HTML.  
Ví dụ trong JavaScript:

```javascript
function escapeHTML(str) {
  return str.replace(/</g, "&lt;").replace(/>/g, "&gt;");
}

document.getElementById("comments").innerText = escapeHTML(input);
```
**Content Security Policy (CSP)**: cấu hình HTTP header để chỉ cho phép script từ nguồn đáng tin cậy.

Không dùng `innerHTML` bừa bãi → ưu tiên textContent hoặc innerText.

**Cách phòng chống CSRF Token:** Mỗi form/request phải kèm một token ngẫu nhiên, server kiểm tra trước khi xử lý.

Ví dụ gửi form kèm token:

```html
<form action="/transfer" method="POST">
  <input type="hidden" name="csrfToken" value="abc123xyz" />
  <button type="submit">Chuyển tiền</button>
</form>
```
`SameSite Cookie:` cấu hình cookie chỉ gửi trong cùng domain.

```
Set-Cookie: sessionid=abc123; HttpOnly; Secure; SameSite=Strict
```
Xác thực lại hành động quan trọng: yêu cầu nhập lại mật khẩu/OTP.

### Ứng dụng thực tế
- Các mạng xã hội, sàn thương mại điện tử thường là mục tiêu hàng đầu.

- Google, Facebook, Twitter đều có hệ thống bảo vệ XSS & CSRF cực chặt.

- Nếu làm ứng dụng web, bạn cần tích hợp bảo mật từ đầu, không để đến khi phát triển xong mới thêm.

### Kết luận
XSS và CSRF là hai lỗ hổng nguy hiểm, phổ biến và dễ khai thác.

XSS: xảy ra do không lọc dữ liệu người dùng.

CSRF: xảy ra do lợi dụng cookie/session của người dùng.

**👉 Muốn xây dựng ứng dụng web an toàn, hãy luôn:**

- Escape dữ liệu đầu vào/đầu ra.

- Sử dụng CSP, SameSite cookie.

- Thêm CSRF token vào các form.

🚀 Khi bạn nắm vững và phòng chống được XSS & CSRF, ứng dụng sẽ bảo mật hơn rất nhiều, giảm thiểu rủi ro cho cả người dùng lẫn hệ thống.
