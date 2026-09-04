# Formal Test Design Techniques (Equivalence Partitioning, BVA, Decision Table)

> **Tài liệu QA Standard**: Phương pháp thiết kế bộ TestCase chuẩn mực.

Các kỹ thuật thiết kế test chính thống.Kỹ thuật Black-Box (Hộp đen): Tập trung vào chức năng và dữ liệu, bao gồm: Phân vùng tương đương (Equivalence Partitioning), Phân tích giá trị biên (Boundary Value Analysis), Bảng quyết định (Decision Tables), Kiểm thử chuyển trạng thái (State-Transition Testing).  Kỹ thuật White-Box (Hộp trắng): Dựa trên cấu trúc nội tại của mã nguồn, tập trung vào việc bao phủ các câu lệnh (Statement Coverage) và nhánh quyết định (Decision/Branch Coverage) để phơi bày các điểm bất thường về mặt logic.  

## 1. EQUIVALENCE PARTITIONING & BOUNDARY VALUE ANALYSIS (BVA)
- **Equivalence Partitioning**: Chia giá trị tiền nhập vào 3 vùng: Hợp lệ (>0), Bằng 0 (=0), Không hợp lệ (<0).
- **BVA (Boundary Value Analysis)**: Test tại các điểm ranh giới: `-1`, `0`, `1`, `MAX_SAFE_INTEGER`.
