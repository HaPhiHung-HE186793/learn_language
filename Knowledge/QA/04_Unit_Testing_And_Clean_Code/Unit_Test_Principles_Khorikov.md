# Khorikov Unit Testing Principles & Mocking Best Practices

> **Tài liệu Unit Test Advanced**: Khi nào dùng Mock/Stub, Domain Model Unit Testing.

Chuẩn mực cho các bài kiểm thử tự động (đặc biệt là Unit Test).Bốn Trụ cột của một bài Test tốt: 1. Bảo vệ chống lại lỗi hồi quy (Protection against regressions).
2. Chống lại sự tái cấu trúc (Resistance to refactoring): Bài test phải tiếp tục pass khi mã nguồn bên trong bị thay đổi nhưng hành vi bên ngoài vẫn giữ nguyên.
3. Phản hồi nhanh (Fast Feedback).
4. Dễ bảo trì (Maintainability).  Hạn chế Mocking: Lạm dụng mock có thể che giấu lỗi và làm bài test bị gắn chặt (couple) vào chi tiết triển khai (vi phạm trụ cột số 2). Chỉ nên mock các phụ thuộc bên ngoài tiến trình (out-of-process) hoặc shared dependencies như external services. Không mock những thư viện bên thứ ba mà bạn không sở hữu; hãy viết một Adapter mỏng bọc lấy nó và mock cái Adapter đó. 

## 1. MOCKING VS STUBBING RULES
- **Mocks**: Kiểm tra tương tác đầu ra (Out-process dependencies như Email, SMS service).
- **Stubs**: Giả lập dữ liệu đầu vào (In-process dependencies).
- Tuyệt đối không mock Domain Models hoặc Value Objects.
