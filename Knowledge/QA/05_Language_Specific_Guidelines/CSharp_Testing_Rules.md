# C# / .NET Testing Rules: xUnit, Moq & FluentAssertions

> **Tài liệu Testing Language-Specific**: Tiêu chuẩn viết unit test C#.

---Ngữ cảnh cho các dự án sử dụng C#/.NET.Công nghệ khuyên dùng: Sử dụng xUnit làm Test framework, Moq để tạo mock objects, và FluentAssertions để viết các câu lệnh assert dễ đọc.  Tách biệt Positive và Negative Cases: Khi dùng Parameterized Tests (ví dụ: [Theory], [InlineData]), phải tách biệt rõ ràng các case thành công (positive) và thất bại (negative) ra thành các theories khác nhau, không trộn lẫn trong cùng một test

## 1. XUNIT & FLUENTASSERTIONS STANDARD
- Sử dụng `xUnit` với `[Fact]` và `[Theory]` (Data-driven tests).
- Viết Assertions dạng đọc tự nhiên: `result.Should().BeEquivalentTo(expected);`.
