# Advanced SQL for Financial Analytics & Reporting

> **Tài liệu SQL Analytics**: Window Functions, CTEs, Aggregation, và Running Totals.

Sức mạnh của SQL: SQL là công cụ không thể thiếu để chuẩn bị dữ liệu, phát hiện giá trị ngoại lai và giải quyết các bài toán phân tích phức tạp trong Data Warehouse.  Window Functions (Hàm cửa sổ): Cho phép tính toán tổng lũy kế, trung bình động và xếp hạng các hàng trên các phân vùng dữ liệu cụ thể mà không làm gộp (collapse) các hàng cơ sở.  Cohort Analysis (Phân tích tập hợp): Sử dụng SQL để nhóm người dùng thành các tập hợp (ví dụ: theo ngày đăng ký) và theo dõi sự thay đổi hành vi, mức độ duy trì khách hàng theo thời gian.  Text Analysis & Regular Expressions: Phân tích dữ liệu văn bản không cấu trúc, trích xuất các thuật ngữ chính và phân loại tương tác của khách hàng bằng biểu thức chính quy (Regex) trực tiếp trong SQL.  Anomaly Detection (Phát hiện bất thường): Sử dụng các hàm thống kê để tính toán độ lệch và tự động phát hiện các giá trị ngoại lai (outliers).  ---


## 1. WINDOW FUNCTIONS TRONG BÁO CÁO TÀI CHÍNH
- **Running Total Balance**: Tính tổng tiền lũy kế theo ngày:
```sql
SELECT 
  created_at,
  amount,
  SUM(amount) OVER (PARTITION BY wallet_id ORDER BY created_at ASC) as running_balance
FROM transactions;
```
- **Month-over-Month Growth (MoM)**: Dùng `LAG()` window function để so sánh tăng trưởng chi tiêu so với tháng trước.
