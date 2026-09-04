# Secure Architecture, Threat Modeling & Defense-in-Depth

> **Tài liệu Security Architecture**: Nguyên tắc Defense-in-Depth, Principle of Least Privilege, và Data Encryption.

Định hướng tư duy thiết kế hệ thống ở quy mô lớn.Đặc quyền Tối thiểu (Least Privilege) & Zero Trust: Cấp cho mỗi thành phần hoặc người dùng mức quyền thấp nhất đủ để thực hiện chức năng được chỉ định. Việc kiểm tra ủy quyền (authorization) phải được thực thi nghiêm ngặt trên mọi yêu cầu tại ranh giới phía máy chủ, không bao giờ dựa vào trạng thái phía client.  Kiến trúc Phân tầng (Tiered Architectures): Trong kiến trúc nhiều tầng (ví dụ: giao diện người dùng, logic nghiệp vụ, cơ sở dữ liệu), cần giảm thiểu sự tin cậy giữa các tầng. Mỗi tầng phải thực thi các biện pháp kiểm soát truy cập riêng (defense in depth) và chạy dưới các tài khoản hệ điều hành có đặc quyền thấp nhất biệt lập với nhau.  An toàn Mặc định (Secure by Default) & Đơn giản hóa: Thiết kế hệ thống phải cấu hình từ chối quyền truy cập theo mặc định (deny-by-default). Việc giữ thiết kế đơn giản và giảm bớt các cơ chế chia sẻ không cần thiết sẽ giúp giảm thiểu rủi ro xuất hiện lỗ hổng.

## 1. PRINCIPLE OF LEAST PRIVILEGE
- API Endpoints luôn kiểm tra `userId` khớp với thông tin trong JWT payload: `where: { id, userId: req.userId }`.
- Không bao giờ tin tưởng Client data. Mọi payload phải đi qua Schema Validation (Zod).

---

## 2. DATA AT REST & DATA IN TRANSIT ENCRYPTION
- **In Transit**: Bắt buộc HTTPS / TLS 1.3 cho tất cả các giao tiếp client-server.
- **At Rest**: Mã hóa dữ liệu nhạy cảm trong DB (như Refresh Token, Secret Keys) bằng AES-256-GCM.

## 3. Mobile bearer credential storage `[Updated 2026-08-25]`

- Web/PWA không lưu bearer credential trong JavaScript storage; dùng host-only `HttpOnly` cookie và CSRF control.
- Native WebView không được xem `localStorage`, `sessionStorage` hoặc Capacitor Preferences là secure storage. JavaScript injection có thể đọc các nguồn này.
- Android lưu ciphertext AES-256-GCM trong private SharedPreferences; khóa AES được tạo và giữ non-exportable trong Android Keystore. Mỗi lần ghi phải có IV ngẫu nhiên mới và authentication tag GCM.
- Credential API ở TypeScript phải bất đồng bộ và phụ thuộc interface bridge, không phụ thuộc trực tiếp Android implementation. Điều này cho phép iOS thay bằng Keychain mà không sửa API/Auth consumers.
- Migration từ bản cũ chỉ được đọc plaintext token trong một module chuyên trách, ghi thành công vào vault trước rồi mới xóa plaintext. Native vault lỗi phải fail closed và yêu cầu đăng nhập lại; tuyệt đối không fallback lâu dài về plaintext.
- Auto Backup có thể restore ciphertext nhưng không restore hardware-backed key. Decrypt failure phải xóa ciphertext vô dụng và buộc re-authentication.
- Với app tài chính có server là source of truth, tắt Android backup và exclude toàn bộ app domains khỏi cloud backup/device transfer. Không backup WebView cookies/cache, private preferences hoặc encrypted credential ciphertext.

## Commercial tenant-isolation gate `[Updated 2026-08-25]`

- Không suy luận ownership coverage từ một endpoint đại diện. Duy trì cross-tenant matrix qua từng object family và kiểm tra authoritative database state sau request, kể cả endpoint cố ý trả idempotent `200` cho object không tồn tại.
- Runtime và migration database transport đều phải explicit TLS. Migration connection trực tiếp không được trỏ vào pooler; launch validator và CI phải fail trước deploy nếu topology sai.
- Legacy logging safety net phải xuất một JSON envelope hữu hạn thay vì chuyển tiếp nhiều argument thô. Error ở production chỉ giữ `name` và bounded machine `code`; identifier/financial key, email, bearer/JWT và secret query param phải bị redact trước sink.
- Structured logger phải ghi thẳng vào native sink đã capture trước khi cài console guard. Nếu gọi lại wrapped `console.*`, log sẽ bị double-wrap và mất event/queryability.

## Client cache tenant boundary `[Updated 2026-08-25]`

- API cache chứa dữ liệu tài chính là một tenant boundary, không chỉ là tối ưu hiệu năng. Không có partition hợp lệ thì không cache.
- Cache invalidation phải chống TOCTOU: generation guard ngăn request của phiên cũ ghi hoặc trả detached cached response sau logout, 401 hay mutation.
- Partition nguồn phải bounded và cache key chỉ giữ digest; nonce cache không có quyền auth nhưng vẫn không nên lộ trong CacheStorage URL.
- Cache correctness là security property: financial 4xx phải đi xuyên và evict cached representation. Chỉ transient failure được fallback với metadata, tuổi tối đa theo data class và streaming body cap.
- SWR/RAM cache cũng cần session generation guard; logout ở tab khác là security event. Không để request cũ commit sau boundary hoặc để React auth state của tab cũ tiếp tục hiển thị dữ liệu.
