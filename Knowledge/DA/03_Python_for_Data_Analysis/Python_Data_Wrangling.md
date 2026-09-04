# Python Data Wrangling with Pandas & NumPy

> **Tài liệu Data Science**: Biến đổi dữ liệu tài chính với Pandas.

Hệ sinh thái cốt lõi: Sử dụng NumPy để tính toán mảng nhanh chóng và thư viện pandas để thao tác trực quan với dữ liệu có cấu trúc/dạng bảng.  Cấu trúc dữ liệu Pandas: DataFrame là cấu trúc dữ liệu dạng bảng hai chiều có nhãn hàng và cột; Series là mảng một chiều có nhãn.  Tính toán Vector hóa: Tránh sử dụng vòng lặp (for loops) thông thường. Pandas tận dụng mảng NumPy để thực hiện các phép tính vector hóa nhanh chóng bằng các thư viện C và Fortran được tối ưu hóa.  Xử lý dữ liệu khuyết thiếu (Missing Data): Pandas sử dụng NaN làm dấu chuẩn cho dữ liệu bị thiếu; sử dụng các hàm như dropna() để lọc hoặc fillna() để điền dữ liệu khuyết.  

## 1. PANDAS FINANCIAL ANALYSIS
- Làm sạch dữ liệu giao dịch trùng lặp (`drop_duplicates`).
- Phân nhóm chi tiêu theo danh mục (`groupby('category').sum()`).
- Time-series resampling (`resample('ME').sum()`) để vẽ biểu đồ xu hướng hàng tháng.
