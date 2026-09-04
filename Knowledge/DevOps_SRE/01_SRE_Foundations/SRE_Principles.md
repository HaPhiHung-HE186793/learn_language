# SRE Principles, SLO/SLI/SLA & Incident Management

> **Tài liệu SRE Senior**: Quản lý độ tin cậy hệ thống (Reliability), Chỉ số SLO/SLI, Error Budgets, và Quy trình ứng phó sự cố (Incident Management).

Quản lý Sự cố và PostmortemMô hình Quản lý Sự cố (IMAG): Google sử dụng hệ thống IMAG (Incident Management at Google), dựa trên Hệ thống Chỉ huy Sự cố (ICS) của Hoa Kỳ. Trọng tâm của mô hình này nằm ở "3 chữ C": Phối hợp (Coordinate), Giao tiếp (Communicate) và Kiểm soát (Control).  Các Vai trò (Roles): Sự cố được quản lý theo cấu trúc phân cấp để tránh xung đột:  Incident Commander (IC): Người có thẩm quyền cao nhất, điều phối toàn bộ các nỗ lực xử lý.  Communications Lead (CL): Chịu trách nhiệm cập nhật thông tin cho các bên liên quan để đội ngũ kỹ thuật tập trung vào việc xử lý.  Operations Lead (OL): Trực tiếp quản lý việc sửa lỗi và triển khai các giải pháp kỹ thuật.  Blameless Postmortem (Mổ xẻ sự cố không đổ lỗi): Sau khi sự cố được giải quyết, một tài liệu postmortem phải được viết ngay lập tức để ghi lại quá trình diễn ra sự cố, nguyên nhân và bài học.  Văn hóa không đổ lỗi: Tổ chức giả định rằng mọi cá nhân đều làm việc với ý định tốt dựa trên thông tin họ có tại thời điểm đó. Thay vì đổ lỗi cho lỗi con người, nhóm SRE tìm kiếm các lỗ hổng hệ thống và đưa ra hành động khắc phục để ngăn sự cố tái diễn.  

## 1. SLO / SLI / SLA & ERROR BUDGETS
- **SLI (Service Level Indicator)**: Chỉ số đo lường hiệu năng thực tế (VD: Tỷ lệ response 200/201 đạt 99.9%).
- **SLO (Service Level Objective)**: Mục tiêu độ tin cậy cam kết nội bộ (VD: Latency < 200ms cho 95% request).
- **SLA (Service Level Agreement)**: Cam kết pháp lý / hợp đồng với khách hàng.
- **Error Budget**: Khoảng cho phép lỗi (100% - SLO). Khi Error Budget hết, dừng release feature mới để tập trung fix reliability!

---

## 2. INCIDENT MANAGEMENT & POST-MORTEM
1. **Triaging & Severity Matrix**: SEV-1 (System Down), SEV-2 (Feature Broken), SEV-3 (Minor Bug).
2. **Post-Mortem Rule (Blameless)**: Mọi sự cố sản xuất đều phải viết Blameless Post-Mortem ghi rõ Root Cause, Timeline, và Remediation Actions.
