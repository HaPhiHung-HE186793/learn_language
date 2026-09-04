# Incident Management & Blameless Post-Mortem Workflow

> **Tài liệu DevOps Operations**: Quy trình xử lý sự cố hạ tầng và sản xuất.

Khái niệm và Nguyên tắc cốt lõi của SRESRE là gì: SRE (Site Reliability Engineering) là một cách tiếp cận cụ thể để triển khai DevOps do Google khởi xướng. SRE áp dụng các phương pháp kỹ thuật phần mềm để giải quyết các vấn đề vận hành hệ thống.  Văn hóa DevOps: DevOps là một phong trào thay đổi văn hóa và quy trình làm việc nhằm thu hẹp khoảng cách giữa phát triển (Dev) và vận hành (Ops), phụ thuộc nhiều vào tự động hóa thay vì các thao tác thủ công.  Chấp nhận Rủi ro (Embracing Risk): Mục tiêu độ tin cậy 100% là điều không thể và quá tốn kém đối với hầu hết các hệ thống. Việc thiết lập mục tiêu dưới 100% cho phép tổ chức phân bổ nguồn lực để đổi mới thay vì chỉ duy trì sự hoàn hảo.  Quản lý Toil (Công việc chân tay): "Toil" là các công việc vận hành lặp đi lặp lại, thủ công và thiếu giá trị kỹ thuật dài hạn. Nguyên tắc của SRE là giới hạn Toil ở mức tối đa 50% thời gian làm việc của kỹ sư.  Thời gian cho Kỹ thuật (Engineering): 50% thời gian còn lại của SRE phải được dành cho việc lập trình các dự án nhằm cải thiện quy mô hệ thống, bảo mật và tự động hóa.  

## 1. QUY TRÌNH 4 BƯỚC ỨNG PHÓ SỰ CỐ
1. **Detect**: Giám sát tự động phát hiện cảnh báo (Grafana / PagerDuty / Sentry).
2. **Respond & Mitigate**: Rollback deployment gần nhất hoặc kích hoạt Circuit Breaker.
3. **Resolve**: Deploy bản sửa lỗi khẩn cấp (Hotfix).
4. **Post-Mortem**: Phân tích nguyên nhân gốc rễ (5 Whys) và tạo Bug Log ticket.
