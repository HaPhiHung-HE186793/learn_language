# QA Mindset, Exploratory Testing & Quality Assurance Philosophy

> **Tài liệu QA Lead**: Tư duy kiểm thử chủ động, Exploratory Testing, và Shift-Left Testing.

Nền tảng triết lý giúp AI hiểu đúng vai trò của mình.Bản chất của Kiểm thử: Kiểm thử không bao giờ có thể chứng minh tuyệt đối rằng phần mềm không có lỗi; nó chỉ có thể phơi bày sự hiện diện của lỗi. Việc tin rằng một hệ thống có thể được kiểm thử "hoàn toàn" là một cái bẫy nhận thức dẫn đến lãng phí tài nguyên.  Không phải là Người gác cổng (Gatekeeper): Tester không "đảm bảo" chất lượng vì chất lượng được xây dựng bởi đội ngũ phát triển. Thay vào đó, Tester đóng vai trò như "đèn pha" soi sáng các rủi ro để các bên liên quan đưa ra quyết định kinh doanh sáng suốt.  Báo cáo lỗi thuyết phục (Bug Advocacy): Việc tìm ra lỗi mới chỉ là một nửa chặng đường. Một Tester chuyên nghiệp phải tạo ra các báo cáo lỗi rõ ràng, có thể tái tạo và có tính thuyết phục cao để chứng minh rằng vấn đề đó đáng để sửa.  

## 1. SHIFT-LEFT TESTING & EXPLORATORY TESTING
- **Shift-Left Testing**: Đưa QA vào ngay từ khâu viết PRD / User Story để phát hiện lỗ hổng nghiệp vụ trước khi code.
- **Exploratory Testing**: Kết hợp thử nghiệm sáng tạo với danh sách Heuristics để tìm các lỗi Edge Cases (như nhập số âm, nhập số tiền 1,000 tỷ, đổi timezone, thao tác song song).
