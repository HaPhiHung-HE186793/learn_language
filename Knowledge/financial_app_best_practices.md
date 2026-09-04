# Best Practices for Personal Finance Applications (Senior Level)

## 1. Xử lý tiền tệ (Currency Handling)
- **Tuyệt đối không dùng kiểu `Float` hay `Double`** để lưu trữ tiền tệ. Lỗi làm tròn số có thể gây thất thoát.
- Luôn sử dụng kiểu dữ liệu số nguyên (Integer/BigInt) để lưu giá trị tiền tệ (ví dụ: lưu 1.00 USD dưới dạng 100 cents), hoặc dùng thư viện chuyên dụng như `decimal.js` / `BigDecimal`.
- Nếu hỗ trợ đa tiền tệ, phải lưu currency exponent và tỷ giá/version/effective time tại thời điểm giao dịch; không hard-code tỷ giá rồi cộng trực tiếp nhiều currency. Khi ledger chưa có các primitive này, launch một currency duy nhất và fail closed tốt hơn “multi-currency giả”.
- `BigInt` trong DB chưa đủ nếu public API trả JSON `number`: JavaScript chỉ biểu diễn chính xác số nguyên trong `[-(2^53-1), 2^53-1]`. Phải dùng cùng một miền tại HTTP validator, backup/restore và DB CHECK constraint; nếu không, writer nền hoặc direct SQL vẫn có thể tạo giá trị bị làm tròn khi serialize.
- Constraint phải bao phủ cả số tiền gốc, số dư tích lũy và snapshot `balanceAfter/resultingAmount`. Kiểm thử integration cần chứng minh giá trị biên được nhận, phép cộng vượt biên fail với constraint violation và statement lỗi không làm đổi row.
- Nếu sản phẩm cần giá trị lớn hơn miền safe integer hoặc tiền có phần thập phân, đổi contract API sang decimal string có version; không âm thầm ép `BigInt` sang `Number`.
- Giới hạn từng row không bảo vệ `SUM`: hai amount hợp lệ vẫn có thể tạo tổng vượt miền JSON. Aggregate phải cộng bằng BigInt, kiểm tra kết quả cuối và chỉ chuyển sang Number một lần. Các tổng hợp tiếp theo như `assets = wallet + savings + receivables` cũng phải tiếp tục ở BigInt, không cộng các Number đã convert.
- Công thức tài sản ròng phải dùng một domain helper duy nhất cho mọi endpoint: `liquid = wallets + savings`, `assets = liquid + receivables`, `netWorth = assets - payables`. Tránh hai route tự triển khai rồi cho kết quả khác nhau.

## 2. Bảo mật dữ liệu (Data Security & Privacy)
- Thông tin tài chính cá nhân là dữ liệu cực kỳ nhạy cảm. Toàn bộ API phải được xác thực bằng JWT (Access/Refresh tokens).
- Không bao giờ log dữ liệu nhạy cảm (số dư, số thẻ, password) ra console hay server logs.

## 3. Nguyên tắc kế toán (Accounting Principles)
- Tuân thủ một phần nguyên tắc ghi sổ kép (Double-entry bookkeeping) hoặc luồng giao dịch rõ ràng: Mọi giao dịch (Transaction) phải xác định rõ Nguồn tiền (Source Wallet) và Đích đến (Destination Category/Wallet).

## 4. Webhook ordering cho subscription entitlement `[Updated 2026-08-25]`

- Idempotency theo payload hash chỉ chống exact replay, không chống hai snapshot khác nhau đến sai thứ tự. Với billing, event cũ ghi đè event mới có thể cấp hoặc thu hồi quyền sai.
- Dùng timestamp version do provider sở hữu (`subscription.attributes.updated_at`), persist cùng subscription và chỉ chấp nhận snapshot mới hơn. Timestamp bằng nhau nhưng payload khác phải fail-safe như stale/conflict.
- Serialize theo provider subscription ID bằng transaction-level advisory lock. Việc đọc version rồi update mà không khóa vẫn có race khi nhiều replica xử lý đồng thời.
- Ghi event stale vào durable inbox với outcome hữu hạn để retry tiếp theo dedupe được và vận hành có audit; không lưu raw payment payload.
- Reconciliation API và webhook phải dùng cùng version/lock invariant, đồng thời so sánh lại version sau khi lấy lock.
