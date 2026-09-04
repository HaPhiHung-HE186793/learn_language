# Effective TypeScript Practices & Type Safety

> **Tài liệu Senior TS**: Xây dựng Type System chặt chẽ, Tránh `any`, và Generics trong React Application.

Ép AI tư duy theo kiểu dữ liệu cấu trúc (Structural Typing) thay vì định danh, tận dụng sức mạnh của trình biên dịch.Type Erasure: Typescript chỉ tồn tại ở quá trình biên dịch (compile-time) và bị xóa hoàn toàn ở runtime; không được dùng type để check logic lúc chạy.  Structural Typing (Duck Typing): TypeScript so sánh các kiểu dữ liệu dựa trên hình dạng (shape) cấu trúc thay vì tên khai báo (nominal).  Avoid 'any': Hạn chế tối đa any; sử dụng unknown cho các giá trị chưa biết kiểu và dùng type guards để thu hẹp phạm vi (narrowing).  Nominal Simulation: Mô phỏng nominal typing bằng Branded Types (ví dụ: type Brand = ID & { __brand: "b" }) để ngăn chặn việc hoán đổi các giá trị giống nhau về cấu trúc nhưng khác ý nghĩa.  Exhaustiveness Checking: Sử dụng kiểu never trong câu lệnh switch/case để đảm bảo mọi nhánh logic đều được xử lý khi thêm type mới. 

## 1. QUY TẮC TYPE SAFETY CỨNG
- **KHIẾN LỖI HIỂN THỊ TẠI COMPILE TIME**: Tuyệt đối không dùng `any`. Dùng `unknown` nếu chưa rõ type và narrowing type bằng type guards.
- **Strict Discriminated Unions**: Phân biệt các loại giao dịch `EXPENSE | INCOME | TRANSFER` bằng Discriminated Unions.
- **BigInt Serialization**: Khi trả JSON từ Backend xuống FE, `BigInt` phải được serialize sang `number` hoặc `string` để tránh lỗi `TypeError: Do not know how to serialize a BigInt`.
