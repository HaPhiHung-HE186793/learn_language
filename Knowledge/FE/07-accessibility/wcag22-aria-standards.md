# Accessibility (a11y) & WCAG 2.2 Standards

> **Tài liệu Senior FE**: Đảm bảo ứng dụng đạt chuẩn Web Content Accessibility Guidelines (WCAG 2.2 AA).

Bộ quy tắc bắt buộc về A11y, giúp AI luôn tạo ra UI tiếp cận được với mọi người dùng.First Rule of ARIA: Không lạm dụng ARIA; ưu tiên sử dụng thẻ HTML gốc có sẵn ngữ nghĩa và hành vi chuẩn.  Modal & Dialog Focus Trap: Khi mở Modal, tiêu điểm (focus) phải bị "khóa" bên trong; phím Escape phải đóng được modal và focus phải trả về nút gọi nó ban đầu.  Focus Not Obscured (WCAG 2.2 AA): Đảm bảo phần tử đang được focus không bị che khuất bởi các thành phần khác (như sticky header) bằng cách dùng scroll-margin-top.  Target Size (WCAG 2.2 AA): Vùng chạm tương tác (pointer inputs) tối thiểu phải đạt kích thước 24x24 CSS pixel để tránh click nhầm.  Redundant Entry (WCAG 2.2 A): Tự động điền hoặc cung cấp lại thông tin người dùng đã nhập trong một quy trình nhiều bước để giảm tải nhận thức

## 1. CORE ACCESSIBILITY RULES
- **Keyboard Navigation**: Tất cả Modal, Dropdown, Button phải điều khiển được bằng phím `Tab`, `Enter`, `Space`, `Escape`.
- **Contrast Ratio**: Độ tương phản chữ và nền tối thiểu `4.5:1` cho text thường và `3:1` cho text lớn.
- **ARIA Attributes**: Gắn `aria-expanded`, `aria-haspopup`, `aria-label` cho các nút icon không có nhãn chữ.
- **Focus Management**: Khi mở Modal, tự động khóa Focus (Focus Trap) bên trong Modal; khi đóng Modal, trả Focus về nút đã bấm.
