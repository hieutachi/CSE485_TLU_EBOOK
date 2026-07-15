# Phiếu học tập 05 — PDO CRUD

> **Gắn với:** Buổi 4  
> **Repo gợi ý:** `cse485-phieu-05-pdo-users`

---

## 1. Tóm tắt lý thuyết

- MySQL lưu dữ liệu bền vững; PHP nói chuyện qua PDO.
- Prepared Statement (`?` / `:name`) chống SQL Injection.
- CRUD: INSERT / SELECT / UPDATE / DELETE.
- `PDO::ERRMODE_EXCEPTION`, `FETCH_ASSOC`.

---

## 2. Bài tập thực hành

### Chuẩn bị SQL

```sql
CREATE DATABASE IF NOT EXISTS cse485_phieu05
  CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

USE cse485_phieu05;

CREATE TABLE users (
  id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50) NOT NULL UNIQUE,
  email VARCHAR(100) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Yêu cầu code

File `config.php` (hàm `db(): PDO`) + `index.php`:

1. INSERT **một** user cố định:
   - `username = 'demo_sv'`
   - `email = 'demo_sv@student.edu.vn'`
2. Nếu username đã tồn tại → bỏ qua insert (bắt exception hoặc check trước), không làm trắng trang.
3. SELECT tất cả users, in bảng HTML: `id | username | email | created_at`.

### Input

User như trên (chạy trang lần đầu → thêm; lần sau → không nhân đôi nếu bạn xử lý đúng unique).

### Output kỳ vọng

Bảng có **ít nhất 1 dòng** `demo_sv` / `demo_sv@student.edu.vn`. Video phải quay thấy dòng này.

---

## 3. Box Yêu cầu nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP BÀI PHIẾU 05                                            ║
╠══════════════════════════════════════════════════════════════╣
║  1. GitHub Repo RIÊNG (không commit mật khẩu DB thật)        ║
║  2. README ghi rõ tên DB + cách import SQL                   ║
║  3. Video OBS + camera mặt: phpMyAdmin/SQL + chạy PHP + bảng ║
║  4. Nộp link Repo + Drive                                    ║
╚══════════════════════════════════════════════════════════════╝
```
