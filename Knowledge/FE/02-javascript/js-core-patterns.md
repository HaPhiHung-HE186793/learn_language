# JavaScript Core Patterns, Event Loop & Event Bus

> **Tài liệu Senior FE**: Pattern xử lý bất đồng bộ, Event Bus Pub/Sub, và Memory Management.

Định hướng AI viết mã JS cốt lõi, áp dụng các design pattern chuẩn và quản lý bộ nhớ.Event Loop & Async: Xử lý bất đồng bộ thông qua Promises; luôn nhớ Microtasks (như Promise.then) chạy trước Macrotasks (như setTimeout).  Lexical Closure: Sử dụng closures để duy trì quyền truy cập vào biến từ phạm vi bên ngoài, phục vụ cho việc đóng gói dữ liệu (data privacy) và module pattern.  Prototype Delegation: Tối ưu bộ nhớ bằng cách chia sẻ thuộc tính qua chuỗi prototype thay vì tạo mới, cơ chế tìm kiếm động sẽ duyệt lên trên cho đến khi gặp null.  Module Pattern: Đóng gói dữ liệu private và chỉ phơi bày (expose) các public API cần thiết để tránh ô nhiễm scope toàn cục.  Observer Pattern: Khuyến khích kiến trúc hướng sự kiện (event-driven) để các component giao tiếp lỏng lẻo (loose coupling).  

## 1. CUSTOM EVENT BUS FOR RE-RENDER & CACHE INVALIDATION
- Sử dụng `CustomEvent` cho việc phát sự kiện thay đổi dữ liệu giữa các Component độc lập:
```typescript
export function notifyDataChanged(scope: string = 'all') {
  window.dispatchEvent(new CustomEvent('app:data-changed', { detail: { scope } }))
}
```
- Subscribe trong Custom Hook (`useSWR`) và tự động cleanup listener trên `unmount`.

---

## 2. EVENT LOOP & MICROTASK QUEUE
- Sử dụng `setTimeout(..., 0)` hoặc `requestAnimationFrame` để đẩy các tác vụ nặng (như Prefetch Cache) xuống cuối Event Loop, tránh làm giật lag UI đợt render đầu tiên.
