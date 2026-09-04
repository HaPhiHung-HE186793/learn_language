# Infrastructure as Code (IaC) & Terraform Best Practices

> **Tài liệu DevOps IaC**: Quản lý hạ tầng đám mây dạng code với Terraform.

Khái niệm về Cơ sở hạ tầng dưới dạng CodeĐịnh nghĩa IaC: Là phương pháp quản lý, triển khai và cập nhật cơ sở hạ tầng (như máy chủ, mạng, cơ sở dữ liệu) thông qua việc viết mã thay vì phải cấu hình thủ công qua giao diện hoặc câu lệnh. IaC coi mọi khía cạnh của việc vận hành như là phát triển phần mềm.  Cơ sở hạ tầng Bất biến (Immutable Infrastructure): Thay vì thay đổi trực tiếp cấu hình trên các máy chủ đang chạy (Mutable), phương pháp này loại bỏ hoàn toàn các máy chủ cũ và thay thế bằng các máy chủ mới được tạo từ cấu hình cập nhật. Điều này giúp loại bỏ tình trạng "rác cấu hình" (configuration drift).  Lợi ích của IaC: * Tài liệu hóa (Documentation): Code chính là tài liệu hiển hiện rõ nhất về trạng thái hạ tầng.  Quản lý phiên bản (Version Control): Toàn bộ lịch sử thay đổi hạ tầng được lưu lại, cho phép rollback dễ dàng khi có lỗi.  Kiểm định (Validation): Việc áp dụng code cho phép chạy các bài kiểm thử tự động, đánh giá tĩnh và code review để giảm thiểu lỗi.  

## 1. TERRAFORM BEST PRACTICES
- **Remote State & Locking**: Lưu `.tfstate` trên S3 / GCS với DynamoDB / Redis State Locking để tránh xung đột khi làm việc nhóm.
- **Modular Design**: Tách hạ tầng thành các Modules tái sử dụng (`vpc`, `rds`, `k8s_cluster`).
- **Secret Management**: Tránh commit Passwords / Keys vào Git. Sử dụng Vault hoặc AWS Secrets Manager.
