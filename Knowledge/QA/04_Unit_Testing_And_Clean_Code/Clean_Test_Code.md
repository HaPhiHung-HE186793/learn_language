# Clean Test Code & Unit Testing Principles (Vladimir Khorikov)

> **Tài liệu Unit Test Senior**: 4 trụ cột của Unit Test tốt theo Khorikov: Protection against regressions, Resistance to refactoring, Fast feedback, Maintainability.

Nguyên tắc viết mã sạch áp dụng riêng cho automation code.Mẫu AAA (Arrange - Act - Assert): Mọi bài test nên được cấu trúc rõ ràng thành 3 phần. Arrange thiết lập trạng thái, Act thực hiện hành vi, và Assert xác minh kết quả. Chỉ nên có MỘT chu kỳ Act-Assert trong một bài test.  DAMP thay vì DRY: Trong mã test, nguyên tắc DAMP (Descriptive and Meaningful Phrases - Các cụm từ mô tả và có ý nghĩa) quan trọng hơn DRY (Don't Repeat Yourself). Ưu tiên tính dễ đọc và rõ ràng của ngữ cảnh thay vì cố gắng loại bỏ hoàn toàn sự lặp lại.  Prefactoring: Tái cấu trúc mã nguồn hiện tại trước khi triển khai tính năng mới (hoặc test mới) vào một Pull Request riêng biệt, giúp code review nhanh hơn và cách ly rủi ro.

## 1. 4 TRỤ CỘT UNIT TEST CHUẨN
1. **Protection Against Regressions**: Test kiểm tra được nhiều mã nghiệp vụ quan trọng.
2. **Resistance to Refactoring**: Test không bị hỏng vô lý khi refactor cấu trúc code bên trong mà kết quả trả về không đổi.
3. **Fast Feedback**: Chạy nhanh hàng ngàn test cases trong vài giây.
4. **Maintainability**: Code test sạch, dễ đọc, cấu trúc Arrange-Act-Assert (AAA) rõ ràng.
