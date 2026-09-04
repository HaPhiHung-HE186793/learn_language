# dbt (data build tool) Project Structure & Data Transformation

> **Tài liệu Data Engineering**: Biến đổi dữ liệu mô hình ELT với dbt.

Mô hình ELT: Sử dụng kiến trúc ELT (Extract, Load, Transform), tải dữ liệu thô vào kho trước rồi dùng dbt làm lớp chuyển đổi trực tiếp trên Data Warehouse.  Cấu trúc dự án 4 lớp chuẩn:Sources Layer: Kết nối với dữ liệu thô, được khai báo qua file .yml.  Staging Layer: Có mối quan hệ 1:1 với bảng nguồn, dùng để làm sạch nhẹ, đổi tên cột, ép kiểu dữ liệu; tuyệt đối không thực hiện JOIN ở lớp này.  Intermediate Layer: Tiêu thụ dữ liệu từ lớp Staging để thực hiện các phép JOIN phức tạp, logic kinh doanh và tổng hợp.  Marts Layer: Lớp cuối cùng chứa các bảng Fact và Dimension hoàn chỉnh, sẵn sàng cho các công cụ BI.  Tiêu chuẩn quy ước: Tên bảng và cột phải viết theo chuẩn snake_case và phải gắn hậu tố mô tả rõ ràng (ví dụ: tiền tệ cần có hậu tố như price_in_cents).  

## 1. MÔ HÌNH STAGING - INTERMEDIATE - MART
- **Staging (`stg_transactions.sql`)**: Clean & rename raw columns từ database.
- **Intermediate (`int_monthly_balances.sql`)**: Gom nhóm tính toán trung gian.
- **Marts (`fct_financial_health.sql`)**: Bảng dữ liệu cuối cùng phục vụ BI Dashboard.
