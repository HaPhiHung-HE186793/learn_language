# Security Ops, Incident Response & Penetration Testing

> **Tài liệu Security Ops**: Quy trình xử lý sự cố an ninh mạng, Audit Logging, và Security Monitoring.

Quy tắc để AI viết test case và thiết lập kịch bản ứng phó sự cố.DevSecOps & Tự động hóa: Tích hợp kiểm tra bảo mật sớm vào quy trình phát triển. Tránh tình trạng "vibe coding" (phụ thuộc hoàn toàn vào AI sinh code mà không kiểm tra) bằng cách sử dụng các công cụ phân tích mã tĩnh (SAST) và luôn có con người đánh giá.  Kiểm thử Chủ động (Offensive Testing): Thiết kế kịch bản Fuzzing (Fuzz testing) để tự động đẩy dữ liệu dị dạng vào ứng dụng nhằm tìm lỗi tràn bộ đệm hoặc xử lý ngoại lệ. Các bài kiểm thử phải mô phỏng cả ranh giới client-side và server-side.  Giám sát & Quản lý Sự cố: Ghi nhật ký (logging) tất cả các sự kiện xác thực và kiểm soát truy cập một cách an toàn mà không để lộ dữ liệu nhạy cảm (như mật khẩu hoặc token). Chuẩn bị sẵn vòng đời phản hồi sự cố theo tiêu chuẩn NIST SP 800-61, theo dõi rủi ro thông qua khuôn khổ MITRE ATT&CK

## 1. AUDIT LOGGING & SECURITY EVENTS
- Ghi log tất cả các hành vi thay đổi quyền, đăng nhập thất bại, đổi mật khẩu, hoặc xoá dữ liệu hàng loạt.
- Không ghi vết các thông tin nhạy cảm như Mật khẩu raw, JWT Secret, Token vào log file.

---

## 2. INCIDENT RESPONSE PLAN
1. **Identify & Contain**: Cô lập tài khoản / IP vi phạm.
2. **Eradicate & Recover**: Sửa lỗ hổng, revoke token bị lộ, khôi phục từ backup sạch.
3. **Post-Mortem**: Phân tích nguyên nhân gốc rễ và cập nhật tài liệu Security Knowledge.
