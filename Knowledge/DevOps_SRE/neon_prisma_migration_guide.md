# Neon + Prisma Migration — Production Deployment Guide

> **Dự án**: app-tai-chinh
> **Stack**: Prisma v7 + Neon PostgreSQL (pooler) + Render
> **Cập nhật**: 2026-08-05

---

## 1. Kiến trúc kết nối Neon

```
Render Server
    ↓
Neon Pooler (PgBouncer)     ← URL có "-pooler" trong hostname
    ↓
Neon PostgreSQL Server      ← Direct connection (không có "-pooler")
```

**Quan trọng**: Neon Pooler (PgBouncer) **KHÔNG hỗ trợ đầy đủ advisory locks**.

---

## 2. Quy trình Migration Production (Neon)

### Bước 1: Tạo migration SQL file

```bash
# Tạo file trong prisma/migrations/YYYYMMDDHHMMSS_name/migration.sql
# Dùng IF NOT EXISTS / IF EXISTS để idempotent
```

### Bước 2: Chạy migration trên Neon

```powershell
$env:DATABASE_URL = "postgresql://...@...-pooler...neon.tech/neondb?sslmode=require"
npx prisma db execute --file ./prisma/migrations/MIGRATION_NAME/migration.sql
```

### Bước 3: Đánh dấu migration đã applied

Tạo file `mark_migration.sql`:
```sql
INSERT INTO "_prisma_migrations" (id, checksum, migration_name, finished_at, applied_steps_count)
VALUES (gen_random_uuid(), 'manual_applied', 'MIGRATION_NAME_HERE', NOW(), 1)
ON CONFLICT DO NOTHING;
```

```powershell
npx prisma db execute --file ./prisma/mark_migration.sql
# Sau đó xóa file mark_migration.sql
```

### Bước 4: Push code → Render auto deploy

`prisma migrate deploy` sẽ thấy migration đã applied → skip → start server.

---

## 3. Troubleshooting: Advisory Lock Timeout (P1002)

### Triệu chứng
```
Error: P1002
Context: Timed out trying to acquire a postgres advisory lock
(SELECT pg_advisory_lock(72707369)). Timeout: 10000ms.
```

### Nguyên nhân
- Advisory lock bị "stuck" (treo) trên Neon server
- Xảy ra khi: `prisma migrate resolve`, `prisma migrate deploy`, hoặc bất kỳ Prisma command nào cần advisory lock bị timeout giữa chừng
- Neon pooler (PgBouncer) có thể không cleanup lock đúng cách khi connection drop

### Fix
1. Vào **Neon Dashboard** → **SQL Editor**
2. Chạy:
```sql
SELECT pg_advisory_unlock_all();
```
3. **Render Dashboard** → **Manual Deploy**

### Phòng tránh
- ❌ **KHÔNG dùng** `prisma migrate resolve` qua Neon pooler URL
- ❌ **KHÔNG dùng** `prisma migrate deploy` từ local qua pooler URL
- ✅ **Dùng** `prisma db execute --file` để chạy SQL trực tiếp (không cần advisory lock)
- ✅ Insert vào `_prisma_migrations` bằng SQL thay vì `migrate resolve`

---

## 4. Render Start Command

```
npx prisma migrate deploy && npm start
```

- `prisma migrate deploy`: Kiểm tra và chạy pending migrations
- Nếu tất cả migration đã applied → skip → start server bình thường
- Chỉ fail nếu advisory lock bị stuck → fix bằng `pg_advisory_unlock_all()`

---

## 5. Checklist khi thêm Migration mới

- [ ] Tạo migration SQL file (idempotent với IF NOT EXISTS)
- [ ] Test trên local DB trước
- [ ] Chạy `prisma db execute --file` trên Neon
- [ ] Insert record vào `_prisma_migrations` trên Neon
- [ ] Git push → kiểm tra Render deploy thành công
- [ ] Nếu fail P1002 → chạy `pg_advisory_unlock_all()` trên Neon SQL Editor

## 6. Commercial migration safety `[Updated 2026-08-25]`

- Không dùng `db push` để thay đổi production và không sửa migration đã được apply; cả hai làm mất tính kiểm toán hoặc tạo checksum drift.
- Không tự insert `_prisma_migrations`/đánh dấu applied trừ quy trình recovery đã được review và có bằng chứng SQL thực sự đã chạy. Đây không phải deployment path bình thường.
- Migration chạy một lần ở pre-deploy/release phase, tách khỏi web start để restart hoặc horizontal scale không đua migration.
- CI phải dựng PostgreSQL cùng major version production, chạy `prisma migrate deploy` từ database sạch rồi `prisma migrate status`. `prisma validate` không chứng minh migration SQL thực thi được.
- Khi lịch sử cũ thiếu một thay đổi nhưng không được phép sửa checksum, thêm migration repair có timestamp trước migration phụ thuộc nếu chưa release commit đó; dùng guard idempotent (`IF NOT EXISTS`) để tương thích database đã từng `db push`.
- Với migration đã release và đang fail ngoài production, dừng rollout, restore/clone để diễn tập, rồi chọn roll-forward migration/recovery có audit trail; không tự ý chỉnh `_prisma_migrations` trên live database.
- Thêm CHECK/foreign key trên bảng lớn theo expand/validate: `ADD CONSTRAINT ... NOT VALID` bảo vệ write mới ngay, sau đó `VALIDATE CONSTRAINT` quét dữ liệu cũ với lock nhẹ hơn inline validation. Vẫn phải đo thời gian/lock trên production clone và đặt change window.
- Index thương mại mới dùng `CREATE INDEX CONCURRENTLY`; thay đổi type, `SET NOT NULL`, DROP/TRUNCATE phải tách expand/backfill/contract và có review/rollback riêng. Source verifier chỉ là ratchet cú pháp, không chứng minh lock runtime của provider.

## 7. Executable data preflight trước migration `[Updated 2026-09-04]`

Các điều kiện dữ liệu có thể làm `VALIDATE CONSTRAINT` thất bại không nên chỉ tồn tại dưới dạng câu SQL trong runbook. Đóng gói chúng thành executable preflight và nối trực tiếp trước `prisma migrate deploy` để mọi môi trường dùng cùng một fail-closed path.

- Preflight phải dùng direct database URL, timeout hữu hạn và application name riêng; không log URL, row content hoặc giá trị tài chính.
- Database mới chưa có bảng phải được nhận diện rõ và cho phép migration-from-zero tiếp tục.
- Database hiện hữu vi phạm chỉ trả bounded counts/machine reason, dừng trước Prisma và yêu cầu kế hoạch remediation có audit.
- CI cần unit-test nhánh fresh/clean/violation, đồng thời migration job chạy path thật trên PostgreSQL sạch.
- Không bypass `npm run deploy:migrate` bằng Prisma CLI trực tiếp. Các hướng dẫn legacy ở mục 3–5 về chạy SQL/ghi `_prisma_migrations` thủ công chỉ dành cho incident recovery đã được review; không phải release workflow chuẩn.
