# PHP Security Guidelines: Modern Web Protection

> **Tài liệu Security Core**: Bảo mật ứng dụng PHP / Laravel.

Cấu hình Môi trường: PHP có một số cấu hình nguy hiểm cần được tắt bỏ để đảm bảo an toàn, ví dụ như register_globals và magic_quotes. Không phụ thuộc vào safe_mode như một biện pháp bảo mật cốt lõi.  Bao hàm Tệp (File Inclusion): PHP rất dễ mắc các lỗi Local/Remote File Inclusion (LFI/RFI) nếu sử dụng các biến đầu vào từ người dùng (như mảng $_GET, $_POST) đưa trực tiếp vào các hàm như include() hoặc require().  Mảng Dữ liệu (Array Variables): Phải xác định và làm sạch cẩn thận mọi dữ liệu lấy từ các mảng đầu vào của PHP trước khi xử lý logic hoặc truy vấn

## 1. LARAVEL & PHP SECURITY BEST PRACTICES
- Chống SQL Injection bằng Eloquent ORM & Prepared Statements.
- Chống CSRF bằng `@csrf` token / `VerifyCsrfToken` middleware.
- Cấu hình `session.cookie_httponly = 1` và `session.cookie_secure = 1` trong `php.ini`.
