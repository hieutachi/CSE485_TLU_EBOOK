# Phiếu 04 — PDO CRUD bảng `categories`

| | |
|---|---|
| **Buổi** | 4 — đọc kèm [Buổi 04](../04-buoi-04-pdo-crud.md) |
| **Thời lượng** | ≥ 45–60 phút |
| **Repo** | `cse485-ms-04` |
| **Nhận từ Phiếu 03** | 3 DM MiniShop đang nằm trong mảng/object — giờ **lưu MySQL** |
| **Mang sang Phiếu 05** | Cùng DB/schema; tách MVC |
| **DB** | `minishop_cse485` |

### Chuẩn đầu ra (CLO)

1. Giải thích được vì sao cần CSDL thay vì chỉ mảng/Session.  
2. Kết nối PDO đúng options; mọi SQL động dùng **prepare/execute**.  
3. CRUD đủ trên `categories` + xử lý trùng `name`.  
4. Có `schema.sql` + README chạy được trên máy khác.

---

## 1. Lý thuyết — từ “nhớ trong RAM” đến “lưu ổ cứng”

### 1.1. Vấn đề sau Phiếu 03

| Lưu bằng | Tắt Apache / F5 / đổi máy |
|----------|---------------------------|
| Mảng trong `data.php` | Còn (nhưng sửa tay file) |
| `$_SESSION` | Mất khi hết session / trình duyệt khác |
| **MySQL** | Còn — nhiều user cùng đọc/ghi |

```
Browser ──HTTP──► PHP ──PDO──► MySQL (categories)
                      ▲
                      └── rows → PHP → HTML bảng
```

### 1.2. PDO là gì?

**PDO** (PHP Data Objects) = lớp chuẩn để nói chuyện với CSDL.

| Cách nguy hiểm | Cách đúng |
|----------------|-----------|
| `"SELECT ... WHERE name='$name'"` | `prepare('... WHERE name = ?')` + `execute([$name])` |

**SQL Injection (hiểu dân dã):** kẻ xấu gửi chuỗi làm “gãy” câu SQL. Prepared statement **tách** khung SQL khỏi dữ liệu → an toàn hơn nhiều.

### 1.3. CRUD — bốn chữ cần thuộc

| Chữ | SQL | Việc UI |
|-----|-----|---------|
| **C**reate | `INSERT` | Form thêm |
| **R**ead | `SELECT` | Bảng list |
| **U**pdate | `UPDATE` | Form sửa |
| **D**elete | `DELETE` | Nút xóa |

### 1.4. Options PDO nên nhớ

```php
PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,  // lỗi → Exception
PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
PDO::ATTR_EMULATE_PREPARES => false,
```

Charset: **`utf8mb4`** (tiếng Việt + emoji an toàn hơn `utf8` cũ).

### 1.5. Phạm vi phiếu — đừng nhảy cóc

**Chỉ bảng `categories`.**  
Bảng `products` + FK → Phiếu 08 (Laravel). Object `Product` ở P03 vẫn là bước tư duy, chưa đưa vào MySQL ở đây.

---

## 2. Bài trên lớp (~20') — DB + kết nối + list + create

### Bước 1 — Tạo DB & bảng (`schema.sql`)

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

Seed 3 dòng (phpMyAdmin hoặc SQL):

| name | description |
|------|-------------|
| Ban phim | Danh muc ban phim co / membrane |
| Chuot | Danh muc chuot may tinh |
| Man hinh | Danh muc man hinh |

### Bước 2 — `config.php`

Hàm `db(): PDO` — host `127.0.0.1`, db `minishop_cse485`, user `root`, pass XAMPP (thường trống).

Dùng `static $pdo` để không mở kết nối mới mỗi lần gọi (pattern đơn giản).

### Bước 3 — `list.php`

```php
$rows = db()->query('SELECT id, name, description, created_at FROM categories ORDER BY id')
            ->fetchAll();
```

In bảng HTML; escape `name`, `description`.

### Bước 4 — Form thêm (cùng trang hoặc `create.php`)

POST → `INSERT INTO categories (name, description) VALUES (?, ?)`.

**Checkpoint:** refresh thấy ≥ 3 dòng từ DB (xóa HTML hardcode nếu còn).

---

## 3. Bài về nhà (~30') — CRUD đủ

### A — Validate Create

- `trim`; tên dài 2–100.  
- Trùng UNIQUE → try/catch `PDOException` hoặc SELECT trước → thông báo gọn, **không** trang trắng.

### B — Update `edit.php?id=`

- `prepare('SELECT * FROM categories WHERE id = ?')`  
- Không tìm thấy → 404/thông báo.  
- POST → UPDATE.

### C — Delete

- Ưu tiên POST + confirm JS.  
- Redirect về list (PRG).

### D — README

Tên DB, user, cách import `schema.sql`, lệnh kiểm `SELECT COUNT(*) FROM categories;`.

### Kịch bản video (đủ điểm)

1. List 3 DM gốc  
2. Thêm `Tai nghe`  
3. Sửa description  
4. Thêm trùng `Chuot` → lỗi thân thiện  
5. Xóa `Tai nghe` → list sạch

---

## 4. Lỗi thường gặp

| Lỗi | Nguyên nhân | Cách xử |
|-----|-------------|---------|
| `Unknown database` | Chưa tạo DB | Chạy schema.sql |
| `Access denied` | Sai user/pass | Kiểm tra XAMPP |
| `SQLSTATE[23000] Integrity` | Trùng name | Bắt exception, báo user |
| Tiếng Việt lỗi | Charset | utf8mb4 + meta UTF-8 |
| Nối chuỗi SQL | Thói quen xấu | Đổi sang `?` placeholder |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 04                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo cse485-ms-04 + schema.sql + README                  ║
║  2. Video CRUD đủ + case trùng tên                           ║
║  3. Thuyết trình: Prepared Statement chống gì? PDO khác gì   ║
║     việc hardcode HTML 3 danh mục?                           ║
╚══════════════════════════════════════════════════════════════╝
```

**Cầu nối Phiếu 05:** cùng CRUD — nhưng tách **Model / Controller / View** (MVC mini).
