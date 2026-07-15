# Phiếu 05 — MVC Mini cho `categories`

| | |
|---|---|
| **Buổi** | 5 |
| **Thời lượng** | ≥ 45 phút |
| **Repo** | `cse485-ms-05` |
| **Nhận từ Phiếu 04** | DB `minishop_cse485.categories` + hiểu CRUD PDO |
| **Mang sang Phiếu 06** | Tư duy Route → Controller → View (Laravel sẽ thay front controller) |

---

## 1. Tóm tắt lý thuyết

```
Request → Front Controller → Controller → Model (PDO) → View (HTML)
```

- Model: SQL, không echo HTML.
- Controller: điều phối, validate nhẹ, redirect.
- View: HTML + biến, không `new PDO`.

---

## 2. Bài trên lớp (~20') — Khung MVC chạy được Index

### Cấu trúc bắt buộc

```
minishop-05/
├── config/database.php
├── models/CategoryModel.php
├── controllers/CategoryController.php
├── views/category/index.php
├── views/category/create.php
├── views/category/edit.php
└── public/index.php          ← Front Controller (Document point)
```

URL mẫu:

```
/public/index.php?controller=category&action=index
/public/index.php?controller=category&action=create
/public/index.php?controller=category&action=edit&id=1
/public/index.php?controller=category&action=delete&id=1
```

### Trên lớp phải xong

1. Whitelist controller/action (không `new $_GET['controller']` tùy ý không kiểm tra).
2. `CategoryModel::all()` + `index` view chạy — list từ DB.
3. Link “Them moi” mở form create (chưa cần submit cũng được nếu hết giờ — nhưng khuyến nghị xong store).

**Checkpoint:** mở thư mục thấy Model **không** chứa thẻ HTML; View **không** gọi `db()`.

---

## 3. Bài về nhà (~30') — CRUD qua MVC + sơ đồ

### Nhiệm vụ A — Đủ Create / Edit / Delete

Port đủ hành vi Phiếu 04 sang MVC:

| Action | Method HTTP | Model |
|--------|-------------|-------|
| index | GET | all() |
| create | GET form + POST store | create() |
| edit | GET form + POST update | find(), update() |
| delete | **POST** (khuyến nghị) hoặc GET + confirm | delete() |

### Nhiệm vụ B — Flash message Session

Sau create/update/delete: set `$_SESSION['flash']` rồi redirect index; view in 1 lần rồi `unset`.

> **Delete:** ưu tiên form POST `action=delete` (đồng bộ thói quen CSRF/DELETE ở Laravel P11–P12). Nếu vẫn dùng GET, bắt buộc `confirm()` và ghi rõ trong README.

### Nhiệm vụ C — File `ARCHITECTURE.md` (trong repo)

Vẽ ASCII (copy & chỉnh) mô tả 1 request **Thêm category**:

```text
Browser POST → index.php → CategoryController::create
    → CategoryModel::create → MySQL
    → redirect index → CategoryModel::all → View list
```

Viết thêm 5–7 dòng: so sánh với 1 file `categories.php` Phiếu 04.

### Nhiệm vụ D — Kiểm thử tự làm

Tạo checklist trong README (tick):

- [ ] Thêm được
- [ ] Sửa được
- [ ] Xóa được
- [ ] View không PDO
- [ ] Model không echo

---

## 4. Tiêu chí “MVC thật” (trọng số chấm)

| Vi phạm | Hậu quả |
|---------|---------|
| SQL nằm trong View | Trượt phần kiến trúc |
| HTML dài trong Model | Trượt phần kiến trúc |
| Front controller không whitelist | Trừ điểm bảo mật cơ bản |

---

## 5. Box nộp bài

```
╔══════════════════════════════════════════════════════════════╗
║  NỘP PHIẾU 05                                                ║
╠══════════════════════════════════════════════════════════════╣
║  1. Repo cấu trúc đúng tên thư mục bắt buộc                  ║
║  2. Video: CRUD qua URL ?controller=&action= + flash message ║
║  3. Mở ARCHITECTURE.md + giải thích 60s                      ║
╚══════════════════════════════════════════════════════════════╝
```
