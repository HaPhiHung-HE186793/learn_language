# Test Data Management & Environment Isolation

> **Tài liệu QA Architecture**: Quản lý dữ liệu kiểm thử an toàn, không nhiễm bẩn DB Production.

Kiến thức về cách tạo môi trường test độc lập và ổn định.Dữ liệu Tham chiếu Dummy: Ưu tiên sử dụng dữ liệu tham chiếu giả (dummy data) cố tình làm khác đi so với dữ liệu thực tế (ví dụ: "Dummy country1" thay vì tên quốc gia thật). Điều này giúp phát hiện ra các giả định ẩn của hệ thống về giá trị dữ liệu và loại bỏ các phụ thuộc không cần thiết.  Chiến lược Cơ sở dữ liệu cho Test: Khởi đầu với một cơ sở dữ liệu trống hoặc rất mỏng, chỉ chứa một thực thể của bảng dữ liệu tham chiếu. Nếu hệ thống quá phức tạp, hãy bắt đầu với DB đầy đủ nhưng xác định rõ dữ liệu nào test cần, tạo dữ liệu đó trong test, và dần dần xóa dữ liệu không liên quan để làm DB mỏng lại.

## 1. STRATEGIES FOR TEST DATA
- **Factory / Seeders**: Sử dụng Prisma Seeders để tạo bộ dữ liệu tiêu chuẩn cho Test Environment.
- **No Production Data in Staging**: Tuyệt đối không copy dữ liệu nhạy cảm của người dùng thật về môi trường Test.
