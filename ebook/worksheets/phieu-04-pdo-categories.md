# Phiếu 04 — PDO CRUD bảng `categories` (nền tảng table 1)

| | |
|---|---|
| **Buổi** | 4 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-04` |
| **Nhận từ Phiếu 03** | 3 danh mục MiniShop (Ban phim / Chuot / Man hinh) |
| **Mang sang Phiếu 05** | Cùng schema + thao tác CRUD sẽ được tách MVC |

### Chuẩn đầu ra (CLO)

1. Tạo DB `minishop_cse485` + bảng `categories` đúng schema phiếu.  
2. CRUD đủ bằng PDO **prepare/execute**.  
3. Xử lý trùng `name` không trang trắng.  
4. Có `schema.sql` trong repo.

---

## 1. Tóm tắt lý thuyết

- MySQL lưu bền vững; PDO + Prepared Statement chống SQL Injection.
- CRUD: INSERT / SELECT / UPDATE / DELETE.
- `ERRMODE_EXCEPTION`, `FETCH_ASSOC`, `utf8mb4`.

> Ở phiếu này chỉ tập trung **1 bảng `categories`**. Bảng `products` sẽ có từ Phiếu 08 (Laravel Migration) — đừng nhảy cóc.

---

## 2. Bài trên lớp (~20') — DB + kết nối + seed

### SQL bắt buộc

```sql
CREATE DATABASE IF NOT EXISTS minishop_cse485
  CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE minishop_cse485;

CREATE TABLE categories (
  id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL UNIQUE,
  description VARCHAR(255) NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Seed 3 dòng (trên lớp, bằng SQL hoặc script PHP)

| name | description |
|------|-------------|
| Ban phim | Danh muc ban phim co / membrane |
| Chuot | Danh muc chuot may tinh |
| Man hinh | Danh muc man hinh |

### Code trên lớp

1. `config.php` với hàm `db(): PDO`.
2. `list.php`: SELECT all, in bảng `id | name | description | created_at`.
3. Form thêm nhanh (POST) trên cùng trang hoặc `create.php` tối giản — INSERT 1 category mới khi submit.

**Checkpoint:** refresh thấy ≥ 3 categories từ DB (không hardcode HTML).

---

## 3. Bài về nhà (~30') — CRUD đủ + validate tối thiểu

### Nhiệm vụ A — Create có kiểm tra

- Tên không rỗng; độ dài 2–100 ký tự.
- Nếu trùng `UNIQUE name` → bắt `PDOException` / check trước, hiện thông báo thân thiện (không trắng trang).

### Nhiệm vụ B — Update

- `edit.php?id=…` load 1 dòng; form POST cập nhật `name`, `description`.
- id không tồn tại → HTTP 404 hoặc thông báo rõ.

### Nhiệm vụ C — Delete

- `delete.php?id=…` (hoặc POST) xóa 1 dòng.  
- Hỏi confirm phía client.
- Sau khi xóa redirect về list.

### Nhiệm vụ D — README vận hành

Ghi trong README: tên DB, user, cách import SQL, screenshot text kết quả `SELECT COUNT(*)`.

### Kịch bản demo video (đủ điểm)

1. List ban đầu 3 danh mục.  
2. Thêm `Tai nghe`.  
3. Sửa description của `Tai nghe`.  
4. Thử thêm trùng tên `Chuot` → báo lỗi.  
5. Xóa `Tai nghe` → list còn đúng dữ liệu.

---

## 4. Ràng buộc an toàn (chấm)

| Không chấp nhận | Bắt buộc |
|-----------------|----------|
| Nối chuỗi SQL với input | `prepare` + `execute` |
| Echo thô `name` | `htmlspecialchars` |
| Lỗi PDO hiện raw dài trên UI | try/catch + message gọn |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 04                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo + file schema.sql                                   ║
║  2. Video CRUD đủ 4 thao tác + case trùng tên                ║
║  3. Thuyết trình: Prepared Statement chống gì?               ║
╚══════════════════════════════════════════════════════════════╝
```
