# Ebook Tự Học: Lập trình Backend với PHP & Laravel

> **Môn học:** CSE485 – Công nghệ Web  
> **Đối tượng:** Sinh viên ngành Công nghệ thông tin  
> **Mức độ:** Từ số 0 đến Admin Dashboard CRUD 2 bảng với Laravel (`belongsTo`)  
> **Đề tài xuyên suốt:** MiniShop (`categories` + `products`)

---

## Lời mở đầu

Chào các bạn sinh viên,

Nếu bạn từng mở một trang web, điền form đăng ký, nhấn **Gửi**, rồi thấy thông báo *"Đăng ký thành công"* — bạn đã chạm vào **Backend** mà không biết. Phần "mặt tiền" đẹp đẽ trên trình duyệt là Frontend. Phần **não bộ** phía sau — nhận dữ liệu, kiểm tra, lưu vào cơ sở dữ liệu, trả kết quả — chính là Backend.

Cuốn Ebook này đi theo lộ trình: PHP thuần → OOP → PDO → MVC mini → Laravel. Lý do rất đơn giản:

> Framework là công cụ. Nếu bạn hiểu bản chất, Laravel sẽ là bạn đồng hành.  
> Nếu bạn chỉ "copy theo tutorial", Laravel sẽ là hộp đen.

Song song 12 buổi là **12 phiếu học tập** (≥ 45 phút/phiếu). Mỗi phiếu có phần **trên lớp** và **về nhà** nối tiếp. Đích cuối phiếu về nhà:

> **Tự xây MiniShop Admin bằng Laravel: 2 bảng liên kết (`belongsTo` / `hasMany`), CRUD đủ cả hai bảng.**

### Mục tiêu xuyên suốt 12 buổi + 12 phiếu

| Giai đoạn | Buổi / Phiếu | Bạn sẽ đạt được |
|-----------|--------------|-----------------|
| PHP thuần | 1–3 / P01–P03 | Catalog mảng → hàm → OOP + Session |
| PDO + MVC | 4–5 / P04–P05 | CRUD `categories` thuần → MVC mini |
| Laravel nền | 6–8 / P06–P08 | Route → Blade Admin → Migration 2 bảng |
| Eloquent | 9–10 / P09–P10 | CRUD model + quan hệ + chống N+1 |
| Dashboard | 11–12 / P11–P12 | Create → **CRUD đủ 2 bảng** (đích) |
| Project nhóm | 13–15 | Nhiều bảng hơn, Auth/Middleware (điểm Cộng) |

### Cách dùng hiệu quả

1. Đọc buổi trên lớp → làm phần **Trên lớp** của phiếu cùng số.
2. Về nhà hoàn thành phần **Về nhà** (≥ 25–40 phút) — giữ nguyên dữ liệu chuỗi MiniShop.
3. Nộp GitHub repo riêng từng phiếu + video OBS (camera mặt).
4. Không nhảy cóc: phiếu sau dựa kết quả phiếu trước.

### Công cụ bạn cần chuẩn bị

- Máy tính (Windows / macOS / Linux)
- [XAMPP](https://www.apachefriends.org/) (Apache + MySQL + PHP)
- [VS Code](https://code.visualstudio.com/) hoặc [Cursor](https://cursor.com/)
- [Composer](https://getcomposer.org/) (từ Buổi 6)
- Tài khoản [GitHub](https://github.com/)
- Phần mềm [OBS Studio](https://obsproject.com/) (quay minh chứng)

---

**Sẵn sàng chưa?** Hãy bắt đầu Buổi 1 — nơi bạn đặt viên gạch đầu tiên của Backend.
