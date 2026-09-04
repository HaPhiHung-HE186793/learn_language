# C/C++ Security Guidelines: Memory Safety & Buffer Overflow Defense

> **Tài liệu Security Core**: Nguyên tắc bảo mật bộ nhớ, phòng chống Buffer Overflow và Memory Leaks trong C/C++.

Quản lý Bộ nhớ (Memory Management): C và C++ yêu cầu lập trình viên tự cấp phát và giải phóng bộ nhớ, khiến chúng dễ mắc các lỗi quản lý bộ nhớ nguy hiểm.  Tràn bộ đệm (Buffer Overflows): Các lỗ hổng như tràn bộ đệm trên Stack (Stack Overflows do dùng các hàm thiếu kiểm tra biên như strcpy) và trên Heap (Heap Overflows) có thể cho phép kẻ tấn công ghi đè bộ nhớ và thực thi mã tùy ý. Cần sử dụng các hàm an toàn hơn và thực hiện kiểm tra biên giới dữ liệu (boundary checking) nghiêm ngặt.  Lỗi chuỗi định dạng & Số nguyên (Format String & Integer Vulnerabilities): Cảnh giác với việc sử dụng sai các hàm định dạng như printf khi input có thể bị kiểm soát bởi người dùng.  

## 1. PHÒNG CHỐNG MEMORY SAFETY ISSUES
- Tuyệt đối tránh các hàm nguy hiểm: `strcpy`, `strcat`, `sprintf`, `gets`. Thay bằng `strncpy_s`, `snprintf`.
- Tránh Use-After-Free bằng RAII và Smart Pointers (`std::unique_ptr`, `std::shared_ptr`).
- Sử dụng AddressSanitizer (`-fsanitize=address`) trong CI pipeline.
