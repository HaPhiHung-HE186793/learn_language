# Versioned legal consent for commercial products

Legal consent là audit boundary, không chỉ là checkbox giao diện. Server phải sở hữu current document metadata; client hiển thị link/version và gửi explicit acceptance; server so khớp exact version ngay trước khi tạo account. Nếu version đổi giữa lúc đọc và submit, trả conflict để user đọc lại.

Lưu timestamp cùng từng document version. Không backfill tài khoản cũ thành đã đồng ý và không sửa historical version khi publish nội dung mới. Một cặp field trên user đủ cho acceptance hiện tại; nếu sản phẩm cần nhiều lần tái chấp thuận có lịch sử, chuyển sang append-only `legal_acceptances` table trước khi bật luồng đó.

Production nên fail-closed khi thiếu pháp nhân, support email, stable version hoặc HTTPS document URLs. Staging smoke phải đọc endpoint public và từ chối placeholder/draft. Code chỉ chứng minh capture/audit mechanics; nội dung, luật áp dụng, processor, retention, refund và consumer rights vẫn cần người có thẩm quyền duyệt.
