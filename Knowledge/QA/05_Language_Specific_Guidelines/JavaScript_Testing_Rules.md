# JavaScript / TypeScript Testing Rules: Vitest, Jest & React Testing Library

> **Tài liệu Testing JS/TS**: Tiêu chuẩn Unit/Integration Test cho Node.js & React App.

---Ngữ cảnh cho các dự án sử dụng JavaScript/TypeScript (UI, Frontend).Dữ liệu Test thực tế: Sử dụng dữ liệu thực tế, giống với production (Realistic data) thay vì các giá trị trừu tượng vô nghĩa như foo hay bar. Điều này biến bộ test thành tài liệu sống mô tả chính xác yêu cầu nghiệp vụ.  Không test các tương tác UI phức tạp trong Unit Test: Tránh viết unit test cho các luồng người dùng phức tạp (như điền form, kéo thả, sau đó submit; hoặc click tab, tải ảnh từ API). Những luồng này nên nhường cho Integration hoặc E2E tests ở các tầng cao hơn của Kim tự tháp kiểm thử. 

## 1. REACT TESTING LIBRARY BEST PRACTICES
- **Query by User-facing Attributes**: Ưu tiên `getByRole`, `getByText` thay vì query theo CSS class hay internal state.
- **User Event simulation**: Sử dụng `@testing-library/user-event` để mô phỏng tương tác người dùng chân thực.
