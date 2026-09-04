# Micro-Frontends Architecture & Module Federation

> **Tài liệu Frontend Architecture**: Mở rộng hệ thống Frontend lớn bằng Micro-Frontends và Module Federation.

Dành cho các dự án mở rộng, đảm bảo AI không tạo ra tính phụ thuộc chéo (tight coupling) giữa các domain.Vertical Slices: Phân tách ứng dụng nguyên khối (monolith) thành các phần tính năng độc lập, từ database đến UI, do các nhóm khác nhau quản lý.  Technology Agnostic: Đảm bảo mỗi nhóm có thể chọn hoặc nâng cấp stack công nghệ riêng; sử dụng Custom Elements (Web Components) để ẩn chi tiết triển khai.  Runtime Isolation: Ứng dụng phải tự chứa (self-contained); nghiêm cấm chia sẻ runtime state hoặc phụ thuộc vào các biến global toàn cục.  Browser-Native Communication: Sử dụng các sự kiện DOM tiêu chuẩn (Browser Events / Custom Events) để giao tiếp giữa các micro-frontends thay vì hệ thống PubSub tùy chỉnh.  Prefixing & Namespacing: Cô lập CSS, Local Storage, Cookies và sự kiện bằng tiền tố (prefix) riêng để tránh xung đột. 

## 1. MICRO-FRONTENDS PRINCIPLES
- **Independent Deployment**: Cho phép các nhóm phát triển và deploy các ứng dụng con độc lập.
- **Module Federation (Webpack/Vite)**: Tải và chia sẻ các shared UI components (`AppModal`, `FormField`) giữa các micro-apps mà không cần đóng gói lại toàn bộ ứng dụng.
