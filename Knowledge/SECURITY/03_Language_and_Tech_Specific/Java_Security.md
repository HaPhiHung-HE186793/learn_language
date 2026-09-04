# Java Enterprise Security Guidelines: Spring Security & JVM Hardening

> **Tài liệu Security Core**: Tiêu chuẩn bảo mật ứng dụng Java / Spring Boot Enterprise.

An toàn SQL: Java cung cấp môi trường có quản lý (managed environment) giúp giảm thiểu lỗi bộ nhớ. Tuy nhiên, để chống SQL Injection, mã Java phải luôn sử dụng java.sql.PreparedStatement (ví dụ: setString, setInt) thay vì sử dụng các chuỗi SQL động.  Mã thực thi (Code Execution): Rất cẩn trọng với các hàm gọi hệ thống và việc khởi tạo các lớp động (Dynamic Code Execution). Cần giới hạn hoặc loại bỏ hoàn toàn quyền truy cập trực tiếp vào hệ điều hành.  Dịch ngược (Decompilation): Mã bytecode của Java có thể dễ dàng bị dịch ngược. Tuyệt đối không lưu trữ các thông tin nhạy cảm, mật khẩu hoặc khóa bảo mật bên trong mã nguồn Java phía client (như applet)

## 1. SPRING SECURITY & OVERDUE DEFENSES
- Bảo mật REST APIs với Spring Security Filter Chain & OAuth2 Resource Server.
- Safe Deserialization: Vô hiệu hóa Gadget Chains, cấu hình `ObjectInputFilter`.
- Connection Encryption: Bắt buộc TLS cho JDBC Database connections (`sslmode=require`).

## 2. Android financial notification boundaries `[Updated 2026-08-25]`

- Không log title/body notification ngân hàng. Nội dung có thể chứa số tiền, số tài khoản, số dư và merchant; debug build cũng không phải nơi an toàn cho raw content.
- Không dùng implicit/dynamic broadcast để chuyển financial payload giữa `NotificationListenerService` và WebView bridge. Trên Android cũ, app khác có thể spoof receiver nếu thiếu signature permission. Ưu tiên callback nội bộ cùng process và durable encrypted queue cho khi process/WebView không hoạt động.
- `MODE_PRIVATE` chỉ là access control, không phải encryption at rest. Queue offline phải dùng AES-256-GCM với IV ngẫu nhiên và key non-exportable trong Android Keystore.
- Giới hạn cả số lượng lẫn tuổi queue; privacy retention vẫn áp dụng cho dữ liệu chưa sync. Corrupt/restored ciphertext phải bị xóa và hệ thống phục hồi bằng queue rỗng.
- Với credential/financial queue cần durability trước khi trả success, kiểm tra kết quả synchronous `commit()` ngoài UI thread thay vì gọi `apply()` rồi giả định dữ liệu đã xuống disk.

## 3. Android commercial release build `[Updated 2026-08-25]`

- Release task phải fail nếu thiếu upload-keystore path/password/alias/key password hoặc versionCode/versionName hợp lệ. Không dùng debug key và không commit `.jks`/password vào repository.
- Bật R8 `minifyEnabled`, resource shrinking, `debuggable=false`; giữ chính xác Capacitor annotations/plugin entry points cần reflection/manifest thay vì tắt shrink toàn cục.
- Native WebView bundle phải require HTTPS API origin tại build time. Relative `/api` trong Capacitor sẽ trỏ vào origin nội bộ và tạo bản build không đăng nhập được.
- Manifest release đặt `usesCleartextTraffic=false`, network security chỉ trust system anchors và tắt backup. FileProvider chỉ expose thư mục receipt cụ thể, không dùng `<external-path path=".">`.
- CI phải tạo ephemeral key chỉ để chứng minh signing/R8/AAB pipeline. Production upload key nằm trong secret manager; Play App Signing, key rotation/recovery và artifact provenance là bước vận hành riêng.
