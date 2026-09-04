# Core Web: Semantic HTML, CSS Architecture & DOM Optimization

> **Tài liệu Senior FE**: Tiêu chuẩn HTML5 Semantic, CSS Architecture (CSS Variables, Flexbox/Grid), và DOM Performance.

Tập trung vào nền tảng cơ bản của trình duyệt, tránh việc lạm dụng JS cho những việc HTML/CSS có thể làm được.Semantic HTML First: Luôn ưu tiên sử dụng các thẻ HTML ngữ nghĩa thay vì dùng thẻ div hoặc lạm dụng ARIA.  Critical Rendering Path: Quá trình render và thực thi script tuân theo mô hình luồng đơn (single-threaded); JS và CSS có thể chặn quá trình hiển thị (render-blocking).  CSS Architecture: Quản lý độ ưu tiên (specificity) bằng Cascade Layers (@layer base, layout, elements) để tránh xung đột style.  Layout Constraint: Căn chỉnh các thành phần con lồng nhau bằng grid-template-columns: subgrid để duy trì hệ thống lưới nhất quán.  

## 1. SEMANTIC HTML & SEO BEST PRACTICES
- Mỗi trang duy nhất 1 thẻ `<h1>`.
- Sử dụng đúng thẻ ngữ nghĩa: `<main>`, `<section>`, `<article>`, `<nav>`, `<aside>`, `<footer>`.
- Interactive elements phải có `id` và `aria-label` duy nhất phục vụ automated testing.

---

## 2. MODERN CSS SYSTEM & DESIGN TOKENS
- Sử dụng Vanilla CSS với HSL Tailwind-like Color Tokens (`--color-primary`, `--color-expense`, `--color-income`).
- Glassmorphism & Micro-animations cho cảm giác Premium WOW.
- Mobile PWA Overlay fix: Sử dụng `position: fixed; inset: 0; z-index: 1000; overflow-y: auto;` cho các Modal trên Mobile Chrome/Cốc Cốc.
