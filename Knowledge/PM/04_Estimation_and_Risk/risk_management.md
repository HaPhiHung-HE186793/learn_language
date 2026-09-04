# Risk Management & Mitigation Strategies

> **Tài liệu Project Management**: Quản lý rủi ro dự án phần mềm.

Nguồn: Waltzing with Bears (Tom DeMarco & Tim Lister)Triết lý về Rủi ro: Một dự án không có rủi ro thì không đáng để làm, vì nó mang lại giá trị rất nhỏ. Bỏ qua rủi ro là tự đưa tổ chức xuống vực.  5 Rủi ro phần mềm cốt lõi: Lỗi lịch trình (schedule flaws), Lạm phát yêu cầu (requirements inflation), Tỷ lệ nghỉ việc (turnover), Đổ vỡ đặc tả (specification breakdown), và Hiệu suất kém (under-performance).  Quản trị rủi ro: Lập dự trữ rủi ro (Risk reserves). Quỹ dự phòng (Contingency reserves) cho các rủi ro đã biết (known-unknowns) và quỹ quản lý (Management reserves) cho các sự kiện không lường trước (unknown-unknowns).  Bài học đắt giá: "Các dự án hoàn thành muộn hầu như luôn luôn là các dự án được bắt đầu quá muộn".  

## 1. QUẢN LÝ RỦI RO KỸ THUẬT & NGHIỆP VỤ
- **Migration Failure Risk**: Luôn có phương án Backup DB snapshot trước khi migrate.
- **Vendor Lock-in Risk**: Trừu tượng hóa các dịch vụ bên thứ ba (SMS Parse, Email Service) qua Interfaces.
