# Exploratory Testing Heuristics & Boundary Edge-Cases

> **Tài liệu QA Lead**: Heuristics cheat-sheet cho ứng dụng quản lý tài chính.

Định hướng AI cách khám phá phần mềm thay vì chỉ chạy script cứng nhắc.Định nghĩa: Kiểm thử khám phá là một quá trình năng động bao gồm việc học hỏi, thiết kế bài kiểm thử và thực thi chúng diễn ra đồng thời. Nó không phải là kiểm thử ngẫu nhiên hay cẩu thả.  Quản lý dựa trên Phiên (Session-Based Test Management - SBTM): Để cấu trúc việc khám phá, hãy sử dụng các phiên kiểm thử có giới hạn thời gian (time-boxed). Mỗi phiên cần có một Tuyên ngôn (Charter) xác định rõ trọng tâm khám phá, ví dụ như bắt đầu với một "recon charter" để hiểu hệ thống.  4 Yếu tố cốt lõi: Thiết kế bài test (tạo ra các biến thể) , Thực thi bài test ngay lập tức , Học hỏi từ các hành vi không mong đợi , và Điều hướng (Steering) cuộc điều tra dựa trên những gì vừa tìm thấy.  

## 1. FINANCIAL APP TEST HEURISTICS
- **Zero & Negative Values**: Nhập số tiền = 0 hoặc âm vào Form thêm giao dịch / ngân sách / chia tiền.
- **Timezone Boundary (UTC vs Local)**: Đổi múi giờ thiết bị sang UTC+0 hoặc UTC-12 rồi tạo giao dịch xem lịch hiển thị có bị lệch ngày không.
- **Race Condition Testing**: Nhấp đôi (Double-click) liên tiếp vào nút "Tạo hóa đơn chia tiền" hoặc "Tất toán nợ".
