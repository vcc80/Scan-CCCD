# Quét & Quản lý CCCD

Ứng dụng web đơn giản:
- Mở trên **điện thoại** → tab "Quét mã" → quét mã QR mặt sau CCCD gắn chip → tự động lưu.
- Mở trên **máy tính** → tab "Danh sách" → thấy ngay bản ghi vừa quét (đồng bộ thời gian thực) → bấm **Copy** để dán vào phần mềm khác.

Không cần cài app, không cần server riêng — chỉ cần trình duyệt + 1 tài khoản Google (Firebase) miễn phí để lưu trữ dữ liệu dùng chung.

---

## Bước 1: Tạo dự án Firebase (miễn phí, ~3 phút)

1. Vào https://console.firebase.google.com → **Add project** (Thêm dự án).
2. Đặt tên bất kỳ (vd: `cccd-scanner`) → Tiếp tục → Tắt Google Analytics nếu không cần → **Create project**.
3. Trong menu bên trái, chọn **Build → Firestore Database** → **Create database**.
   - Chọn **Start in test mode** (chế độ thử nghiệm) → chọn vùng gần bạn (vd: `asia-southeast1`) → **Enable**.
   - ⚠️ Test mode chỉ mở công khai trong 30 ngày, sau đó cần chỉnh lại rule (xem phần "Bảo mật" bên dưới).

## Bước 2: Lấy thông tin cấu hình (config)

1. Ở trang chủ dự án, bấm biểu tượng **`</>`** (Web) để thêm 1 ứng dụng web.
2. Đặt tên app (vd: `cccd-web`) → **Register app**.
3. Firebase sẽ hiện đoạn code có object `firebaseConfig = { apiKey: ..., authDomain: ..., ... }`.
4. Copy toàn bộ các giá trị đó, dán vào file **`config.js`** trong dự án này (thay các dòng `"DÁN_..._VÀO_ĐÂY"`).

## Bước 3: Tải lên GitHub

1. Tạo 1 repository mới trên GitHub (vd: `cccd-scanner`), để **Public**.
2. Tải 3 file này lên repo: `index.html`, `config.js` (đã điền thông tin thật), `README.md`.
   - Cách nhanh nhất: vào repo → **Add file → Upload files** → kéo thả 3 file → **Commit**.

## Bước 4: Bật GitHub Pages

1. Vào repo → **Settings → Pages**.
2. Ở mục **Branch**, chọn `main` (hoặc `master`), thư mục `/ (root)` → **Save**.
3. Đợi khoảng 1 phút, GitHub sẽ cấp cho bạn 1 link dạng:
   `https://<ten-tai-khoan>.github.io/cccd-scanner/`

## Bước 5: Sử dụng

- **Trên điện thoại nhân viên:** mở link trên bằng Safari (iPhone) hoặc Chrome (Android) → cho phép quyền Camera → tab "Quét mã" đang mở sẵn → đưa camera vào mã QR mặt sau CCCD.
- **Trên máy tính:** mở cùng link đó → chuyển sang tab "Danh sách" → thấy bản ghi hiện ra ngay (không cần bấm gì thêm) → bấm nút **📋 Copy tất cả** (copy đủ 7 trường) hoặc bấm **Copy** cạnh từng trường riêng (vd chỉ copy Số CCCD) → dán (Ctrl+V) vào phần mềm cần dùng.
- Có thể lưu link này thành icon trên màn hình chính điện thoại (Safari: Share → "Add to Home Screen") để dùng như 1 app riêng.

---

## Ghi chú quan trọng về bảo mật

Vì đây là dữ liệu cá nhân nhạy cảm (CCCD), sau khi test xong bạn **nên** siết lại quyền truy cập Firestore thay vì để "test mode" (ai có link cũng đọc/ghi được):

- Vào **Firestore Database → Rules**, thay bằng rule yêu cầu đăng nhập, hoặc giới hạn theo IP nội bộ công ty, hoặc bật Firebase Authentication (đăng nhập bằng email nhân viên) rồi đổi rule thành:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /cccd_scans/{doc} {
      allow read, write: if request.auth != null;
    }
  }
}
```

Nếu công ty bạn cần mức bảo mật cao hơn (nhiều người dùng, phân quyền, xóa lịch sử tự động...), nên cân nhắc thêm lớp đăng nhập trước khi dùng chính thức với dữ liệu thật.

## Tùy biến thêm (tùy chọn)

- Muốn tự động xóa dữ liệu sau X ngày → thêm Cloud Function hoặc dọn thủ công trong Firestore Console.
- Muốn xuất ra file Excel → có thể yêu cầu bổ sung nút "Xuất Excel" ở tab Danh sách.
- Muốn giới hạn chỉ nhân viên công ty truy cập → thêm màn hình đăng nhập bằng mật khẩu chung hoặc Firebase Authentication.
