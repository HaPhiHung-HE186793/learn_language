# OWASP Top 10 Web Application Vulnerabilities & Mitigations

> **Tài liệu Security Standard**: Phòng chống SQL Injection, XSS, CSRF, IDOR, và Broken Access Control.

Cung cấp cho AI danh sách các lỗi phổ biến cần phòng tránh khi sinh code.Xác thực Dữ liệu Đầu vào (Input Validation): Tất cả dữ liệu do người dùng cung cấp đều không đáng tin cậy. Phải sử dụng phương pháp "Chấp nhận dữ liệu đã biết là an toàn" (Allow list/Accept Known Good) thay vì cố gắng lọc dữ liệu xấu.  Injection (SQLi, Command Injection): Để ngăn chặn SQL Injection, phải sử dụng các truy vấn có tham số (Parameterized Queries) để tách biệt dữ liệu nhập vào khỏi cấu trúc lệnh SQL. Không bao giờ nối chuỗi (concatenate) trực tiếp input của người dùng vào các câu lệnh hệ điều hành hoặc cơ sở dữ liệu.  Cross-Site Scripting (XSS): Phòng chống XSS bằng cách mã hóa và làm sạch dữ liệu đầu ra trước khi hiển thị trên trình duyệt. Lỗ hổng này thường xảy ra khi dữ liệu người dùng được nhúng vào HTML mà không được xử lý an toàn.  XML eXternal Entity (XXE) & SSRF: Vô hiệu hóa việc phân tích cú pháp các thực thể bên ngoài (external entities) khi xử lý dữ liệu XML để tránh lỗ hổng XXE, có thể dẫn đến việc đọc các tệp tin cục bộ hoặc tấn công SSRF.

## 1. PHÒNG CHỐNG CÁC LỖ HỔNG PHỔ BIẾN
- **SQL Injection**: Sử dụng ORM (Prisma) sử dụng Parameterized Queries. Tuyệt đối không dùng string concatenation trong SQL.
- **Cross-Site Scripting (XSS)**: React tự động sanitize JSX string. Với HTML động, bắt buộc dùng `DOMPurify.sanitize()`.
- **Insecure Direct Object References (IDOR)**: Kiểm tra quyền sở hữu đối tượng trước khi thực hiện READ/UPDATE/DELETE.

```typescript
// ✅ ĐÚNG (Chống IDOR):
const bill = await prisma.groupBill.findFirst({
  where: { id: billId, userId: req.userId }
})
if (!bill) return res.status(404).json({ error: 'Không tìm thấy hóa đơn' })
```
