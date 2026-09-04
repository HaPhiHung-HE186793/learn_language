# AWS / Cloud Well-Architected Framework

> **Tài liệu Cloud Architecture**: 6 Trụ cột đám mây: Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability.

Kiến trúc Đám mây chuẩn mực (Well-Architected Framework)Azure và Google Cloud cung cấp các nguyên tắc xây dựng đám mây xuất sắc thông qua 5 trụ cột cốt lõi:  Độ tin cậy (Reliability): * Hệ thống cần được thiết kế để chịu được các sự cố phần cứng, mạng, hoặc vùng mà không làm ảnh hưởng đến người dùng.  Sử dụng các cơ chế tự phục hồi (auto-healing), dự phòng (redundancy) đa vùng và mở rộng theo chiều ngang (horizontal scaling).  Bảo mật (Security): * Cốt lõi để bảo vệ dữ liệu, chống lại các mối đe dọa. Áp dụng kiến trúc Zero-Trust (không tin tưởng bất kỳ ai, luôn xác minh).  Tuân thủ nguyên tắc đặc quyền tối thiểu (least privilege), mã hóa dữ liệu ở mọi tầng, và kiểm soát quyền truy cập dựa trên hệ thống quản lý nhận dạng (như Azure Active Directory).  Tối ưu Chi phí (Cost Optimization): * Cân bằng giữa chi tiêu đám mây và giá trị kinh doanh.  Cần theo dõi liên tục chi phí, xóa bỏ tài nguyên bị lãng phí và tận dụng các chính sách chiết khấu (như Reserved Instances).  Tối ưu Vận hành (Operational Excellence): * Tập trung vào khả năng tự động hóa việc triển khai (CI/CD) để giảm lỗi và rủi ro.  Sử dụng khả năng giám sát (observability) liên tục để nhận diện sớm lỗi và thiết lập quy trình phản ứng sự cố (Incident Management).  Hiệu suất (Performance Efficiency): * Thiết kế hệ thống có khả năng tự động mở rộng linh hoạt (Auto Scaling) nhằm phản ứng ngay lập tức với sự thay đổi của lượng truy cập.  Điều này giúp hệ thống không bị quá tải (gây sập) cũng như không bị cấp phát dư thừa (gây lãng phí tài nguyên).  

## 1. 6 TRỤ CỘT ĐÁM MÂY
1. **Operational Excellence**: Tự động hóa IaC, CI/CD pipelines.
2. **Security**: Identity & Access Management (IAM), Least Privilege.
3. **Reliability**: Multi-AZ deployments, Auto-healing.
4. **Performance Efficiency**: CDN Edge Caching, Serverless scaling.
5. **Cost Optimization**: Auto-scaling down off-peak, Savings Plans.
6. **Sustainability**: Tối ưu hóa tiêu thụ năng lượng hạ tầng.
