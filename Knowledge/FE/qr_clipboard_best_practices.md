# HTML5 Clipboard API & Dual Text + QR Image Copying Best Practices (Senior FE)

> **Mục tiêu**: Hướng dẫn kỹ thuật chuẩn mực để sao chép đồng thời Tin nhắn Text + Ảnh Mã QR (PNG Blob) vào Khay nhớ tạm (Clipboard) của hệ điều hành, phục vụ dán (`Ctrl + V`) tức thì vào Zalo, Messenger, Telegram hay các ứng dụng OTT.

---

## 1. Bản chất Kỹ thuật của Async Clipboard API

Khi làm việc với HTML5 Async Clipboard API (`navigator.clipboard.write`), ta có thể truyền vào một mảng `ClipboardItem`:

```typescript
const item = new ClipboardItem({
  'text/plain': textBlob,
  'image/png': imageBlob,
})
await navigator.clipboard.write([item])
```

### Điểm mấu chốt (Critical Requirements):
1. **Kiểu MIME của Ảnh**: Các hệ điều hành (Windows, macOS, Android, iOS) và trình duyệt Chromium/Safari **yêu cầu bắt buộc `image/png`** đối với `ClipboardItem`. Nếu đính kèm `image/jpeg` hoặc `image/webp`, Clipboard API có thể bị từ chối (`NotAllowedError` hoặc silent fail).
2. **Biến đổi Base64 / Data URL sang PNG Blob**:
   - Nếu ảnh lưu trữ ở định dạng `data:image/png;base64,...`, ta có thể `fetch(dataUrl)` và chuyển thẳng sang Blob.
   - Nếu ảnh gốc ở định dạng JPG/WEBP/GIF, cần render thông qua `<canvas>` với `canvas.toBlob(..., 'image/png')` để chuyển đổi chuẩn sang PNG Blob trước khi đưa vào `ClipboardItem`.

---

## 2. Snippet Chuẩn Senior (Copy Text + Image PNG)

```typescript
/**
 * Chuyển đổi Data URL (Base64) hoặc Image URL bất kỳ sang PNG Blob tương thích Clipboard API
 */
export async function dataUrlToPngBlob(dataUrl: string): Promise<Blob> {
  if (dataUrl.startsWith('data:')) {
    const res = await fetch(dataUrl)
    const blob = await res.blob()
    if (blob.type === 'image/png') return blob

    return new Promise((resolve) => {
      const img = new Image()
      img.crossOrigin = 'anonymous'
      img.onload = () => {
        const canvas = document.createElement('canvas')
        canvas.width = img.width
        canvas.height = img.height
        const ctx = canvas.getContext('2d')
        ctx?.drawImage(img, 0, 0)
        canvas.toBlob((b) => resolve(b || blob), 'image/png')
      }
      img.onerror = () => resolve(blob)
      img.src = dataUrl
    })
  }
  const res = await fetch(dataUrl)
  return res.blob()
}

/**
 * Thao tác Sao chép Đồng thời Tin nhắn Text + Ảnh Mã QR
 */
export async function copyMessageWithQrImage(messageText: string, qrImageUrl?: string | null): Promise<boolean> {
  let copiedWithQr = false

  if (qrImageUrl && navigator.clipboard && window.ClipboardItem) {
    try {
      const pngBlob = await dataUrlToPngBlob(qrImageUrl)
      const textBlob = new Blob([messageText], { type: 'text/plain' })

      const item = new ClipboardItem({
        'text/plain': textBlob,
        'image/png': pngBlob,
      })

      await navigator.clipboard.write([item])
      copiedWithQr = true
    } catch (err) {
      console.warn('Ghi khay nhớ tạm kèm ảnh thất bại, chuyển sang fallback copy text:', err)
      await navigator.clipboard.writeText(messageText)
    }
  } else {
    await navigator.clipboard.writeText(messageText)
  }

  return copiedWithQr
}
```

---

## 3. Quản lý Trạng thái & Trải nghiệm Người dùng (UX & State)

1. **Ghi nhớ Cấu hình (Persistence)**:
   - Lưu trạng thái Ví được chọn và công tắc Bật/Tắt đính kèm ảnh QR vào `localStorage` (ví dụ `split_bill_wallet_id`, `split_bill_copy_qr`).
   - Giúp người dùng thao tác liên tục mà không cần chọn lại ví hay bật/tắt công tắc thủ công cho từng lượt gửi.

2. **Phản hồi Trực quan (Toast & Notice)**:
   - Hiển thị thông báo rõ ràng khi copy thành công:
     - `✅ Đã sao chép tin nhắn VÀ ảnh mã QR vào khay nhớ tạm!` (khi bật copy QR).
     - `✅ Đã sao chép nội dung tin nhắn!` (khi tắt copy QR để tránh thừa lặp lại ảnh).

---

*Tài liệu tạo: 2026-08-15 | Version: 1.0 — QR Clipboard & OTT App Integration*
