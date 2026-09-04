# Data Architecture & Dimensional Modeling (Star Schema & Snowflake Schema)

> **Tài liệu Data Analytics**: Thiết kế Data Warehouse, Fact Tables, và Dimension Tables cho báo cáo tài chính.

---
Phân biệt hệ thống: Cần tách biệt rõ ràng giữa hệ thống vận hành (nơi nhập dữ liệu) và hệ thống phân tích/kho dữ liệu (nơi lấy dữ liệu ra).  Dimensional Modeling (Mô hình đa chiều Kimball): Là kỹ thuật thiết kế cơ sở dữ liệu tập trung vào khả năng đọc hiểu của người dùng và hiệu suất truy vấn thay vì chuẩn hóa (như 3NF).  Bảng Fact và Dimension: Cấu trúc dữ liệu dựa trên bảng Fact chứa các chỉ số đo lường hiệu suất và bảng Dimension chứa các thuộc tính mô tả, ngữ cảnh.  Enterprise Data Warehouse Bus Architecture: Sử dụng các "conformed dimensions" (chiều chuẩn hóa) và "conformed facts" để tích hợp các data mart phân tán lại với nhau một cách nhất quán.  Surrogate Keys (Khóa thay thế): Luôn tạo và sử dụng khóa thay thế cho các bảng Dimension để tránh phụ thuộc vào khóa của hệ thống nguồn (production keys).  Slowly Changing Dimensions (SCDs): Theo dõi sự thay đổi của dữ liệu theo thời gian (ví dụ: tạo bản ghi mới với khóa thay thế mới thay vì ghi đè làm mất lịch sử).  

## 1. STAR SCHEMA CHO DASHBOARD TÀI CHÍNH
- **Fact Table (`fact_transactions`)**: Chứa các con số đo lường (`amount`, `transaction_date_key`, `wallet_key`, `category_key`).
- **Dimension Tables (`dim_wallets`, `dim_categories`, `dim_dates`)**: Chứa thông tin thuộc tính phân tích.
- **Grain**: Chi tiết đến từng giao dịch cá nhân.
