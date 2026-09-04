# Test Architecture, Test Isolation & Data Setup

> **Tài liệu Test Automation**: Kiến trúc Test Automation Framework, Test Isolation và Mocking.

Kiến trúc tự động hóa chuẩn ISTQB.Kiến trúc Kiểm thử Tự động Tổng quát (gTAA): Đây là thiết kế cấp cao bao gồm 4 giao diện chính: Giao diện SUT (kết nối giữa SUT và Framework) , Giao diện Quản lý dự án (tiến độ) , Giao diện Quản lý kiểm thử (ánh xạ test case) , và Giao diện Quản lý cấu hình (CI/CD pipelines, testware).  Các Tầng của Framework (TAF Layers): Để tránh thiết kế phức tạp, nên giữ số lượng tầng ở mức thấp. Cấu trúc chuẩn bao gồm: Tầng Test Scripts (không gọi trực tiếp core libraries) , Tầng Business Logic (chứa các thư viện phụ thuộc vào SUT) , và Tầng Core Libraries (độc lập với SUT, có thể tái sử dụng).

## 1. TEST ISOLATION PRINCIPLES
- **Database Cleanup per Test**: Mỗi test case phải chạy trên môi trường DB độc lập (dùng SQLite in-memory hoặc Prisma Transactions Rollback sau mỗi test).
- **Test Data Builders**: Sử dụng Builder Pattern để tạo dữ liệu giả lập test data.
