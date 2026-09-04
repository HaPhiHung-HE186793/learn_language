# React Patterns, SWR Hooks & State Management

> **Tài liệu Senior React**: Các Pattern thiết kế Component React, SWR Stale-While-Revalidate Caching, và Synchronized Dual-Mode UI.

Cung cấp các pattern hiện đại nhất cho React/Next.js, cấm AI sử dụng các pattern lỗi thời như HOCs nếu không cần thiết.Hooks Pattern: Ưu tiên dùng Custom Hooks để chia sẻ logic state, thay thế hoàn toàn cho Higher-Order Components (HOCs) nhằm tránh tình trạng "wrapper hell".  Compound Components: Xây dựng các UI phức tạp (Tabs, Accordion, Modal) linh hoạt bằng cách cho phép người dùng kiểm soát cấu trúc thông qua composition.  Islands Architecture: Giảm dung lượng JavaScript tải xuống client bằng cách kết hợp nội dung tĩnh (static HTML) với các "hòn đảo" (islands) tương tác độc lập.  Progressive Hydration: Kiểm soát thứ tự và lịch trình hydrate hóa các component trên client độc lập với nhau, tránh việc block main thread

## 1. SOLID COMPONENT PATTERNS
- **Base Wrapper Pattern (`AppModal`, `FormField`)**: Tất cả Modal mới đều bọc bởi `<AppModal>`, tất cả Form Fields bọc bởi `<FormField>`.
- **DRY API Helper Encapsulation**: Tất cả các lệnh gọi API đều đóng gói trong `src/services/api.ts` (như `reportAPI`, `authAPI`, `debtAPI`). Component UI tuyệt đối không gọi `fetch` trực tiếp.

---

## 2. SYNCHRONIZED SIMPLE & ADVANCED MODES RULE
- **Quy tắc ĐỒNG BỘ 100%**: Khi sửa đổi hoặc thêm tính năng ở **Advanced Mode** (ví dụ: Xuất Excel, Lịch, Đổi Theme), BẮT BUỘC kiểm tra màn hình tương ứng ở **Simple Mode** để gọi chung 1 Service Helper/Hook.
- **Cache Cleansing Before Reload**: Mọi hành động xoá dữ liệu lớn (Reset All, Reset Partial, Restore JSON) BẮT BUỘC phải gọi `clearAllCache()` trước `window.location.reload()` để xoá cả RAM Map & `swr_persist_*` trong LocalStorage.
