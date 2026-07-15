# Phiếu học tập 06 — MVC Mini

> **Gắn với:** Buổi 5  
> **Repo gợi ý:** `cse485-phieu-06-mvc-users`

---

## 1. Tóm tắt lý thuyết

- MVC tách Model (DB), Controller (điều phối), View (HTML).
- Front Controller (`index.php`) chọn controller/action.
- Model không echo HTML; View không kết nối PDO trực tiếp.

---

## 2. Bài tập thực hành

### Yêu cầu

Chuyển logic Phiếu 5 thành **đúng 3 lớp file** (tên bắt buộc):

```
UserModel.php        ← all(), create($username, $email)
UserController.php   ← index(): gọi model + require view
index.php            ← View: chỉ HTML hiển thị $users
```

Được phép thêm `config/database.php` và front controller nếu cần, nhưng phải có đủ 3 tên trên.

### Input

Giống Phiếu 5: đảm bảo có user `demo_sv`.

### Output

Trang list users (HTML table). Trong video: mở lần lượt 3 file và giải thích vai trò từng file.

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 06                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG — cấu trúc MVC rõ ràng                 ║
║  2. Video OBS + camera mặt: chỉ code 3 file bắt buộc + chạy  ║
║  3. Thuyết trình: nếu bỏ MVC thì khó bảo trì chỗ nào?        ║
║  4. Nộp link Repo + Drive                                    ║
╚══════════════════════════════════════════════════════════════╝
```
