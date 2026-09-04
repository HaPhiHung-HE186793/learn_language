# Application Security Foundations & Authentication Architecture

> **Tài liệu Security Senior**: Bảo mật JWT Auth, Password Hashing, Session Management và OWASP Top 10 Protections.

File này chứa các nguyên tắc và tiêu chuẩn bất biến áp dụng cho mọi dự án.Tuân thủ Tiêu chuẩn: Hệ thống cần được thiết kế dựa trên Khung An ninh mạng NIST (CSF) bao gồm các chức năng cốt lõi để phòng thủ và quản lý rủi ro. Áp dụng các biện pháp kiểm soát bảo mật từ NIST SP 800-53 và CIS Critical Security Controls v8.1 để bảo vệ tính bảo mật, tính toàn vẹn và tính sẵn sàng của dữ liệu.  Mô hình hóa Mối đe dọa (Threat Modeling): Việc đánh giá bảo mật phải bắt đầu từ khâu thiết kế bằng phương pháp Mô hình hóa Mối đe dọa của Adam Shostack. Cần xác định rõ các ranh giới tin cậy (trust boundaries) và luồng dữ liệu. Áp dụng phương pháp STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege) để nhận diện các mối đe dọa tiềm ẩn trước khi viết code.  Mật mã học (Cryptography): Phải ưu tiên sử dụng các thuật toán mã hóa hiện đại, đã được kiểm chứng (như AES-GCM, SHA-256) và tuyệt đối không tự tự viết các hàm mã hóa tùy chỉnh (custom crypto). Đảm bảo tính bí mật chuyển tiếp (forward secrecy) và bảo vệ an toàn cho các khóa mật mã. Mật khẩu người dùng phải được băm (hash) kết hợp với salt (ví dụ sử dụng bcrypt hoặc Argon2) thay vì mã hóa hai chiều.

## 1. AUTHENTICATION & PASSWORD HARDENING
- **Password Hashing**: Sử dụng Argon2id hoặc bcrypt (salting + minimum cost 10-12).
- **JWT Protection**:
  - Store JWT trong `HttpOnly`, `Secure`, `SameSite=Strict` Cookie hoặc `localStorage` với short expiration.
  - Refresh Tokens lưu DB có khả năng Revoke lập tức.
- **OTP Verification**:
  - Gửi OTP 6 chữ số có thời hạn (5 phút).
  - Khóa tài khoản / tạm dừng thử sau 5 lần nhập sai liên tiếp.
  - Rate limit: Tối đa 3 yêu cầu OTP / giờ / email.

---

## 2. SECURITY HEADERS & CORS
- Bật `helmet` middleware trên Express/Fastify.
- Cấu hình Strict CORS Origin (`CORS_ORIGIN=https://app-tai-chinh-six.vercel.app`).
