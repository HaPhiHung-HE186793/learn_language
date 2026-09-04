# Terraform Advanced Standards & Code Structure

> **Tài liệu DevOps IaC**: Quy chuẩn đặt tên, State Isolation, và DR.

Kiến trúc và Thực hành TerraformTerraform là gì: Là một công cụ mã nguồn mở sử dụng ngôn ngữ khai báo (declarative language) để tạo và quản lý cơ sở hạ tầng trên nhiều nhà cung cấp đám mây.Cơ chế hoạt động: Terraform lưu trạng thái thực tế của cơ sở hạ tầng vào một file gọi là state. Nó so sánh code khai báo hiện tại với file state và tự động gọi API đám mây để tạo ra hạ tầng mong muốn.Quản lý State từ xa (Remote Backend): Trong môi trường làm việc nhóm, file state bắt buộc phải được lưu trữ ở một máy chủ từ xa có cơ chế khóa (locking), chẳng hạn như kết hợp Amazon S3 và DynamoDB, nhằm ngăn ngừa việc nhiều người cùng sửa đổi gây xung đột dữ liệu.  Cấu trúc thư mục (File Layout): Nên chia nhỏ code theo từng môi trường và từng thành phần để cô lập rủi ro. Ví dụ điển hình:  stage/: Môi trường kiểm thử.prod/: Môi trường sản xuất.global/: Các tài nguyên dùng chung như IAM, S3.Bên trong mỗi môi trường, chia tiếp thành vpc, services, data-storage.Terraform Modules: Để tránh lặp lại code (DRY), mã nguồn cần được đóng gói thành các module nhỏ, dễ kiểm thử và có thể tái sử dụng (như bản thiết kế - blueprints).  

## 1. STATE ISOLATION BY ENVIRONMENT
- Tách biệt môi trường `staging` và `production` ra các Terraform Workspaces hoặc State files riêng biệt.
- Sử dụng `terraform plan` trong CI/CD Pull Request trước khi `apply`.
