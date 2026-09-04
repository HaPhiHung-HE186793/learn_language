# Service Level Objectives (SLO), Indicators (SLI) & Error Budget Management

> **Tài liệu SRE Core**: Cách tính toán và thiết lập SLO, SLI, Error Budgets cho ứng dụng tài chính.

Ngăn xếp Độ tin cậy (The Reliability Stack)Service Level Indicator (SLI): Là một thước đo định lượng cụ thể về hiệu suất hoặc hành vi của dịch vụ. Công thức tính SLI thường là tỷ lệ phần trăm giữa số sự kiện tốt (good events) trên tổng số sự kiện hợp lệ (valid events). Các chỉ số phổ biến bao gồm độ trễ (latency), tỷ lệ lỗi (error rate), độ sẵn sàng (availability) và thông lượng (throughput).  Service Level Objective (SLO): Là mục tiêu hoặc ngưỡng độ tin cậy được thiết lập cho một SLI trong một khoảng thời gian nhất định. SLO đóng vai trò là ranh giới phân định giữa việc người dùng hài lòng và không hài lòng đối với dịch vụ.  Service Level Agreement (SLA): Là một thỏa thuận mang tính kinh doanh hoặc pháp lý giữa nhà cung cấp dịch vụ và khách hàng. SLA quy định mức dịch vụ cam kết và các hình phạt thương mại (như hoàn tiền) nếu không đạt được mục tiêu.  Error Budget (Ngân sách Lỗi): Là lượng thời gian gián đoạn hoặc sự cố hệ thống có thể chấp nhận được mà không làm giảm sự hài lòng của người dùng. Ngân sách lỗi là phần bù toán học của SLO, được tính bằng công thức: $E = 1 - SLO$.  Quản lý Vận hành qua Error Budget: Nếu ngân sách lỗi vẫn còn dư dả, đội ngũ có thể thoải mái triển khai các tính năng mới và thử nghiệm rủi ro. Ngược lại, nếu ngân sách lỗi bị cạn kiệt, mọi nỗ lực triển khai tính năng mới phải dừng lại để dồn toàn lực vào việc khôi phục độ tin cậy.  

## 1. THIẾT LẬP SLI/SLO CHO FINANCIAL APP
- **Availability SLI**: `Successful Requests / Total Requests * 100%` (SLO Target: 99.95%).
- **Latency SLI**: `P99 Latency < 300ms` (SLO Target: 99%).
- **Error Budget Policy**: Khi Error Budget còn lại < 20%, hệ thống tự động khóa pipeline deploy tự động và yêu cầu SRE review.
