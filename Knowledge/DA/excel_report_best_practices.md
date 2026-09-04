# Senior DA/BA Best Practices: Executive Excel Report Generation (.xlsx)

Tài liệu hướng dẫn chuẩn hóa báo cáo Excel xuất từ hệ thống ứng dụng tài chính cho cấp quản lý (Executive/C-Level) và Người dùng lớn tuổi (Elderly/Non-Tech Users).

Thống kê mạnh (Robust Statistics): Đối với các tập dữ liệu bị lệch (skewed), sử dụng các phương pháp như Median Absolute Deviation và khoảng phân vị thay vì số trung bình.  Đường cơ sở dự báo (Baselines): Trước khi triển khai các mô hình dự báo chuỗi thời gian phức tạp, hãy thiết lập các mốc cơ sở rõ ràng bằng các phương pháp cơ bản (naive và seasonal naive).  A/B Testing Guardrails: Thiết lập một Tiêu chí Đánh giá Tổng thể (OEC - Overall Evaluation Criterion) kết hợp giữa các chỉ số thúc đẩy tăng trưởng và rào cản hiệu suất hệ thống.  Tính toàn vẹn của thử nghiệm: Luôn chạy các bài kiểm tra A/A và theo dõi lỗi Sample Ratio Mismatch (SRM) để đảm bảo hệ thống chia luồng hoạt động chính xác

## 🎨 1. Design System & Palette Màu Báo Cáo Excel

Báo cáo Excel xuất từ ứng dụng tài chính **KHÔNG BAO GIỜ** được sử dụng dạng CSV bản thô nền trắng không màu sắc. Phải áp dụng Design System màu sắc ngân hàng/tài chính chuyên nghiệp:

### Palette Màu Chuẩn (Emerald Executive Theme):
| Thành phần | Mã Màu Hex | Mô tả & Công dụng |
|------------|------------|--------------------|
| **Primary Header** | `#059669` (Emerald 600) | Nền tiêu đề bảng chính, chữ trắng in đậm (`#FFFFFF`) |
| **Sub Header / Category** | `#047857` (Emerald 700) | Nền tiêu đề phụ / Tóm tắt chỉ tiêu |
| **Accent Light** | `#ECFDF5` (Emerald 50) | Nền dòng Tóm tắt tổng / Dòng Thống kê chính |
| **Zebra Striping** | `#F8FAFC` (Slate 50) | Nền dòng xen kẽ giúp mắt người lớn tuổi không bị hoa mắt |
| **Income Green** | `#16A34A` (Green 600) | Số tiền Thu nhập / Thặng dư |
| **Expense Red** | `#DC2626` (Red 600) | Số tiền Chi tiêu / Thâm hụt |
| **Warning Orange** | `#D97706` (Amber 600) | Cảnh báo chi tiêu vượt ngân sách |
| **Border Grid** | `#E2E8F0` (Slate 200) | Đường viền ô mỏng sắc nét (`thin` border) |

---

## 📐 2. Quy chuẩn Typography & Layout cho Người lớn tuổi

1. **Font chữ**: Sử dụng `Segoe UI` hoặc `Calibri` (tương thích 100% trên Windows, macOS, Excel, WPS Office).
2. **Kích thước chữ (Font Sizes)**:
   - Tiêu đề Báo cáo / Tên Sổ tay: **16pt Bold**
   - Tiêu đề Phần / Section: **13pt Bold**
   - Header Bảng (Cột): **11pt Bold (Chữ Trắng nền Xanh Emerald)**
   - Dữ liệu bình thường: **11pt Regular**
   - Con số Số tiền / Tổng kết: **11pt - 12pt Bold**
3. **Tự động tính độ rộng cột (Auto-Fit Column Width)**:
   - Thuật toán bắt buộc tính chiều dài của chuỗi lớn nhất trong cột + cộng thêm margin padding `3 - 5` ký tự.
   - Đảm bảo **KHÔNG BAO GIỜ** xảy ra hiện tượng tràn chữ `###` hoặc cắt ngắn tiêu đề (như `Chi trong th`, `Thay đổi tháng`).
4. **Định dạng số tiền (Native Currency Mask)**:
   - Sử dụng định dạng chuẩn Excel: `#,##0 "VNĐ"`
   - Đảm bảo Excel căn phải (`right-align`) số tiền, hiển thị rõ ràng `890.090 VNĐ`, không bao giờ bị cắt xẻ số 0 cuối (như `890.09`).

---

## 💻 3. Cấu trúc Multi-Tab (Nhiều Trang Sổ Excel)

Báo cáo Excel tài chính cá nhân chuẩn senior bao gồm 3 Worksheets (Tabs):

### Tab 1: `📖 Nhật Ký Thu Chi`
- Tiêu đề Banner có màu nền đẹp mắt.
- Khối Thẻ Tóm Tắt (KPI Card): Tổng Thu, Tổng Chi, Số dư Còn lại có chỉ báo màu sắc.
- Bảng Nhật ký chi tiết theo ngày với 8 cột (`STT`, `Ngày tháng`, `Thứ`, `Phân loại`, `Danh mục`, `Số tiền`, `Ví`, `Ghi chú`).

### Tab 2: `📊 Phân Tích Danh Mục`
- Thống kê tổng tiền chi cho từng danh mục (Ăn uống, Sinh hoạt, Mua sắm...).
- % Tỷ trọng chi tiêu + Cảnh báo ngân sách tự động.

### Tab 3: `💳 Ví & Tài Khoản`
- Danh sách tất cả các ví (Tiền mặt, Ngân hàng, Ví điện tử) và số dư thực tế hiện tại.

---

## ⚙️ 4. Code Mẫu ExcelJS Chuẩn Senior Node.js

```typescript
import ExcelJS from 'exceljs'

const workbook = new ExcelJS.Workbook()
workbook.creator = 'Personal Finance App'
workbook.created = new Date()

// Tạo Tab 1
const sheet1 = workbook.addWorksheet('📖 Nhật Ký Thu Chi', {
  views: [{ showGridLines: true }]
})

// Styled Header Row
const headerRow = sheet1.addRow(['STT', 'Ngày tháng', 'Thứ', 'Phân loại', 'Danh mục', 'Số tiền', 'Ví', 'Ghi chú'])
headerRow.font = { name: 'Segoe UI', size: 11, bold: true, color: { argb: 'FFFFFF' } }
headerRow.fill = {
  type: 'pattern',
  pattern: 'solid',
  fgColor: { argb: '059669' }
}
headerRow.alignment = { vertical: 'middle', horizontal: 'center' }

// Format Currency Column
sheet1.getColumn(6).numberFormat = '#,##0 "VNĐ"'

// Auto-Fit Columns Width
sheet1.columns.forEach(column => {
  let maxLen = 12
  column.eachCell!({ includeEmpty: true }, cell => {
    const val = cell.value ? cell.value.toString() : ''
    if (val.length > maxLen) maxLen = val.length
  })
  column.width = Math.min(maxLen + 4, 45)
})
```

---

## 📐 5. Chuẩn Tối Ưu Khung Nhìn (Ideal Viewport) & Excel Auto-Filter

1. **Bật Bộ Lọc Tự Động (Excel Auto-Filter)**:
   - Tất cả bảng dữ liệu nhiều hàng **PHẢI** được kích hoạt sẵn tính năng Bộ lọc tự động trên thanh tiêu đề: `sheet.autoFilter = 'A11:H11'`.
   - Giúp người dùng khi mở file Excel có thể click nút mũi tên lọc ngay theo ngày, thứ, phân loại Thu/Chi, danh mục hoặc ví một cách tức thì.

2. **Chống Tràn Chữ (Wrap Text Control)**:
   - Ô Ghi chú chi tiết **BẮT BUỘC** phải bật `wrapText: true` (`cell.alignment = { wrapText: true }`).
   - Tuyệt đối không để chữ dài tràn sang các ô trống bên cạnh làm rối mắt. Khi chữ dài, Excel tự động xuống dòng gọn gàng bên trong phạm vi Cột Ghi Chú.

3. **Chiều Rộng Cột Lý Tưởng (Ideal Viewport Width - Xem ở Zoom 100%)**:
   - Tổng chiều rộng bảng dữ liệu chuẩn không được vượt quá **115-120 ký tự** để hiển thị trọn vẹn 100% trên mọi màn hình laptop/máy tính bàn ở mức **Zoom 100% / 90%** mà không cần cuộn ngang hoặc zoom nhỏ về 55%:
     * `STT`: width **6**
     * `Ngày tháng`: width **12**
     * `Thứ`: width **10**
     * `Phân loại`: width **10**
     * `Danh mục`: width **18**
     * `Số tiền`: width **16**
     * `Ví / Nguồn tiền`: width **14**
     * `Ghi chú chi tiết`: width **30** (bật `wrapText`)

---

*Tạo ngày: 2026-07-29 | Phiên bản: 2.0 — Enhanced with Auto-Filter, Wrap Text & Ideal Viewport Standards*

