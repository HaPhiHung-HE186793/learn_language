# .NET / C# Security Guidelines: Data Protection & Crypto APIs

> **Tài liệu Security Core**: Tiêu chuẩn bảo mật ứng dụng .NET / C# Enterprise.

Tận dụng Framework: Khung ASP.NET cung cấp nhiều cơ chế phòng thủ tích hợp sẵn (ví dụ: chống XSS) nhưng không được ỷ lại hoàn toàn vào cấu hình mặc định.  Bảo mật Cấu hình: Nếu cần lưu trữ các chuỗi kết nối cơ sở dữ liệu (connection strings) hoặc dữ liệu nhạy cảm trong các tệp cấu hình, phải sử dụng tính năng "protected configuration" của ASP.NET để mã hóa chúng.  View State: Lưu ý rằng các thành phần duy trì trạng thái như ViewState có thể bị thao túng hoặc làm lộ dữ liệu nếu không được mã hóa và xác thực tính toàn vẹn (MAC) đúng cách. 

## 1. .NET DATA PROTECTION API (DPAPI)
- Mã hóa dữ liệu nhạy cảm sử dụng `IDataProtector`.
- Phòng chống Deserialization Vulnerability: Tránh `BinaryFormatter` (deprecated). Sử dụng `System.Text.Json` với type validation.
- SQL Injection Defense: Sử dụng Entity Framework Core LINQ queries.
