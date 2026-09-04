# Growth Loops & Product Roadmap Strategy

> **Tài liệu Growth Product**: Vòng lặp tăng trưởng người dùng (Growth Loops) và Lộ trình sản phẩm.

Triết lý cốt lõi: Lộ trình và Tăng trưởng có hệ thống (Reforge Frameworks)Vòng lặp Tăng trưởng (Growth Loops) thay thế Phễu (Funnels): Các sản phẩm phát triển nhanh nhất sử dụng các hệ thống khép kín (Loops) nơi đầu ra được tái đầu tư thành đầu vào để tạo sự tăng trưởng kép, thay vì phễu tuyến tính.  Khung tăng trưởng 4 Fits: Sản phẩm thành công cần sự liên kết của 4 yếu tố: Market-Product Fit (Thị trường-Sản phẩm), Product-Channel Fit (Sản phẩm-Kênh phân phối), Channel-Model Fit (Kênh-Mô hình kiếm tiền), và Model-Market Fit (Mô hình-Thị trường).  Lộ trình sản phẩm 4D (4D Product Roadmaps): Đánh giá và ưu tiên các sáng kiến qua 4 lăng kính: Chiến lược (Strategy), Tầm nhìn (Vision), Khách hàng (Customer), và Kinh doanh (Business) để đảm bảo lộ trình cân bằng và tạo ra đòn bẩy. 

## 1. VIRAL GROWTH LOOP TRONG CHIA TIỀN NHÓM
- **Feature Trigger**: Người dùng tạo hóa đơn chia tiền ăn uống / du lịch.
- **Action**: Bấm "Sao chép tin nhắn gửi Zalo" cho bạn bè.
- **Viral Output**: Bạn bè nhận được tin nhắn kèm link app → Truy cập và đăng ký app tài chính.
# Commercial Analytics & Monetization Guardrails `[Updated 2026-08-25]`

- Không dùng analytics tài chính nhạy cảm để “tăng conversion”. Chỉ đo hành vi sản phẩm tối thiểu sau consent rõ ràng.
- Tách entitlement khỏi payment provider: provider webhook cập nhật subscription state; domain service quyết định feature access.
- Triển khai paywall ở shadow mode trước để kiểm chứng audience, copy và luồng restore/cancel mà chưa gây lockout.
- Không bật enforcement trước khi webhook signature, idempotency, reconciliation, refund, grace period và customer support flow có test.

## Billing Channel Decision `[Updated 2026-08-25]`

- Web/PWA tại Việt Nam: Lemon Squeezy adapter (Merchant of Record, payout hỗ trợ Việt Nam), nhưng vẫn giữ domain entitlement độc lập provider.
- Android phân phối qua Google Play: dùng Play Billing cho digital subscription; không tái sử dụng Web checkout trong store build.
- iOS App Store: dùng StoreKit/In-App Purchase; web purchase chỉ được nhận diện theo chính sách multiplatform tương ứng.
- Không bật enforcement trước reconciliation job, refund/chargeback mapping, restore purchase và runbook hỗ trợ khách hàng.

Lemon Squeezy subscription policy: `cancelled` vẫn có quyền đến `ends_at`; `paused`, `past_due` và `unpaid` chưa đồng nghĩa hết quyền. Chỉ `expired` chắc chắn thu hồi quyền. `subscription_updated` được dùng làm catch-all; payment events là invoice object và không được đưa vào subscription parser. Tham chiếu: [Subscription object](https://docs.lemonsqueezy.com/api/subscriptions/the-subscription-object), [Webhook event types](https://docs.lemonsqueezy.com/help/webhooks/event-types).
